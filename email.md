## Email

Sending emails is still one of the most common feature for web applications. Designing an email client requires some consideration
- how to trigger email delivery (without coupling the logic)
- how to send email at specific time? (dead-letter queue or cron job)
- how to track emails sent?
- how to deal with invalid email (don't send again)
- how to ensure one-time only delivery or how to prevent sending abuse?
- how to ensure the clients receive the email?
- how to check the optimal time to send email?


## Design consideration


How to share different services/ how to compose services from different modules?
Don’t. In the first place, if they are used together, then group them in the same service. But what if the service depends on another service, which Iis called by another service?
 E.g. mailer service sends a passwordless magic link, which calls the auth service to get authenticate the user and get the token. So mailer > auth Service. Now invite user api wants to send an email to login, which needs to call the mailer service...we have absented hierarchy of service now. By right services should be placed in the same level. We can use a mediator to facilitate or orchestrate the service calls. 
So in order to put them at the same level, we need to extract the use cases out from each service and compose them differently/ externally.

Or consider an email template that returns the pricing information of the product purchasesd. There need to be a logic to that needs to be applied before sending out email.pricing calculation , gst, discounts. Therefore the email client now needs two source, the data source and a business logic layer. We can treat the email template as a view, and apply the modelview ocontroller partttern 

At the very bare, we have a sender/mailer interface
Send(template) error

To trigger send, we can use a dispatch mechanism, or message queue. But this leaves a little to be desired. If the message queue receiver is on another service. It must know how to format the email template before sending them outs unless there is  a way to share the logic, it will be messy. To keep things simple. Place the email sender together with the implementation

 This makes it easier to reason with the logic. 


## Use Cases

