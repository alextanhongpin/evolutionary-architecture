## Designing a notification system

We want to build a news feed system and a notification system. Whenever a user performs an action of interest on a post (e.g. comment, like etc), we want to send the notification to the subscribers of the post. 

## Design

- the system should be able to handle different events
- the system should be able to handle different consumers (mobile push, webhook, email notification)
- the system should aggregate the notifications if there are multiple actions for the same post, e.g. User A, User B liked the post.
- the system should be able to handle hot users (users with 1,000,000 subscribers)

## Code with NodeJS
```js
class EventEmitter {
  constructor() {
    this.events = {}
  }
  on(name, fn) {
    if (!this.events[name]) this.events[name] = []
    this.events[name].push(fn)
  }
  emit(name, params) {
    if (!this.events[name]) return
    const fns = this.events[name]
    for (let fn of fns) {
      fn(params)
    }
  }
}

class PostService extends EventEmitter {
  constructor(postRepository, commentRepository) {
    super()
    this.postRepository = postRepository
    this.commentRepository = commentRepository
  }
  async createPost({
    userId,
    text
  }) {
    const postId = await this.postRepository.create({
      userId,
      text
    })
    return postId
  }
  async createComment({
    userId,
    postId,
    text
  }) {
    const commentId = await this.commentRepository.create({
      postId,
      userId,
      text
    })
    this.emit('commentCreated', {
      userId,
      postId,
      text,
      commentId,
      timestamp: Date.now()
    })
    return commentId
  }
}

class Notification {
  constructor({
    actor,
    target,
    verb,
    time = Date.now(),
    object,
    title,
    summary
  }) {
    this.value = {
      actor,
      target,
      verb,
      object,
      time,
      title,
      summary
    }
  }
}
class ActivityObject {
  constructor({
    id,
    name,
    summary,
    representativeImage,
    permalinkUrl,
    objectType
  }) {
    this.value = {
      id,
      name,
      summary,
      representativeImage,
      permalinkUrl,
      objectType
    }
  }
}

class NotificationRepository {
  constructor() {
    this.notifications = []
    this.id = 0
  }
  create({
    actor,
    target,
    verb,
    object,
    time,
    title,
    summary
  }) {
    this.notifications.push({
      actor,
      target,
      verb,
      object,
      time,
      title,
      summary,
      id: ++this.id
    })
    return this.id
  }
  getNotifications() {
    return this.notifications.slice()
  }
}

class PostRepository {
  constructor() {
    this.posts = []
    this.id = 0
  }
  create({
    userId,
    text
  }) {
    this.posts.push({
      userId,
      text,
      id: ++this.id,
      timestamp: Date.now()
    })
    return this.id
  }
  getPost(postId) {
    const post = this.posts.find(post => post.id === postId)
    return post
  }
}

class CommentRepository {
  constructor() {
    this.comments = []
    this.id = 0
  }
  create({
    userId,
    postId,
    text
  }) {
    this.comments.push({
      userId,
      postId,
      text,
      id: ++this.id,
      timestamp: Date.now()
    })
    return this.id
  }
  getCommenterIds(postId) {
    const comments = this.comments.filter((comment) => comment.postId === postId)
    return comments.map(comment => comment.userId)
  }
}

async function main() {
  const comments = new CommentRepository()
  const posts = new PostRepository()
  const postService = new PostService(posts, comments)
  const notifications = new NotificationRepository()



  postService.on('commentCreated', async ({
    userId,
    postId,
    commentId,
    text,
    timestamp
  }) => {
    console.log(`[User ${userId}] created ${postId} ${text}`)
    const {
      userId: ownerId,
      text: postText
    } = await posts.getPost(postId)
    const commenterIds = await comments.getCommenterIds(postId)

    // Don't send to yourself.
    const receiverIds = commenterIds.concat(ownerId).filter(id => id !== userId)
    // In case there are duplicates.
    const uniqueReceiverIds = [...new Set(receiverIds)]

    const time = Date.now()
    for (let id of uniqueReceiverIds) {
      const activity = new Notification({
        actor: userId,
        target: id,
        verb: 'comment_created',
        time,
        object: new ActivityObject({
          id: postId,
          objectType: 'post',
          summary: postText,
        })
      })
      notifications.create(activity.value)
    }
  })

  const userA = 1
  const userB = 2
  const userC = 3

  const postId = await postService.createPost({
    userId: userA,
    text: 'hello world'
  })
  await postService.createComment({
    userId: userA,
    text: 'hi, any thoughts?',
    postId
  })
  await postService.createComment({
    userId: userB,
    text: 'nope',
    postId
  })
  await postService.createComment({
    userId: userC,
    text: 'haha...funny',
    postId
  })
  await postService.createComment({
    userId: userA,
    text: 'really?',
    postId
  })

  setTimeout(() => {
    const aggregates = notifications.getNotifications().reduce((acc, notification) => {
      const key = `${notification.verb}:${notification.target}`
      if (!acc[key]) {
        acc[key] = []
      }
      acc[key].push(notification)
      return acc
    }, {})

    for (let key in aggregates) {
      const aggregate = aggregates[key]
      const {
        target,
        object
      } = aggregate[0]
      const {
        summary
      } = object.value
      console.log(`sending notification to user ${target}`)
      const actors = aggregate.reduce((acc, agg) => {
        return acc.concat(agg.actor)
      }, [])
      console.log(`${actors.join(', ')} has commented on ${summary}`)
      console.log('')
    }
    console.log(aggregates)
  }, 1000)
}

main().catch(console.error)
```


## References

- http://activitystrea.ms/specs/atom/1.0/#object
