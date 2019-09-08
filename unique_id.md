## How to generate unique IDs in a distributed system

Motivation: There are several advantages of generating unique id. Auto-incrementing the ids would not work in distributed system would not work due to collision in the ids. Having a unique id would also allow us to have better control over the accesses - e.g. the same id cannot be reused, or could be blacklisted. It is common to have a service that will generate this unique id, rather than each node creating their own ids, which could lead to id collision.

References:
- https://www.callicoder.com/distributed-unique-id-sequence-number-generator/
- https://medium.com/@varuntayal/what-does-it-take-to-generate-cluster-wide-unique-ids-in-a-distributed-system-d505b9eaa46e
- https://preparingforcodinginterview.wordpress.com/2017/03/21/unique-id-generation-in-distributed-systems/
- https://www.quora.com/How-can-we-design-a-unique-number-generator-in-distributed-system-without-using-3rd-party-services-existing-libraries
- https://www.slideshare.net/davegardnerisme/unique-id-generation-in-distributed-systems
- https://www.ctl.io/developers/blog/post/server-generated-keys-unique-ids-for-distributed-databases
- http://antirez.com/news/99
- Flick's Ticket Server: http://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/
- How Firebase generate a unique ID: https://firebase.googleblog.com/2015/02/the-2120-ways-to-ensure-unique_68.html
- http://blog.gainlo.co/index.php/2016/06/07/random-id-generator/
- https://preparingforcodinginterview.wordpress.com/2017/03/21/unique-id-generation-in-distributed-systems/
- https://stackoverflow.com/questions/2671858/distributed-sequence-number-generation
- https://www.farfetchtechblog.com/en/blog/post/unique-integer-generation-in-distributed-systems/
- http://yellerapp.com/posts/2015-02-09-flake-ids.html
