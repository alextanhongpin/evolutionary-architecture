How to design a purchase flow?

https://dba.stackexchange.com/questions/105915/modeling-invoices-and-orders

## How to ensure that anonymous user does not break certain flows (purchase, registration etc)? 

Usually in multi-steps form, there are a sequence of actions that needs to be completed before user can proceed with the final action (e.g. purchase/payment). If any of the steps are not completed, user cannot proceed. If the user attempts to skip any steps in between, the system should be able to detect too. 

This is normally simple when the steps are immediate. But it get's complicated when the next step does not execute immediately or depends on another party (e.g. for approval). Take for example a purchase forms where you can share with the people around you (which you invite by email, and they are also validated through the email). They can fill in the form on what they would like to purchase and submit it within the time you set. 

Create a single unique session that will expire (or can only be used once) and send them a link with that token through email. For each steps, assign a unique bits (1, 2, 4, 8 etc). The email link can only be visited once. If the user complete a step, mark it. If they try to break the steps, forbid them, or ask if they want to restart the flow. The flow can only go in one direction, restarting means repeating the steps from the first one. How to avoid spam (user sending it to another email?). By adding more constraints like the sending can only be done every 5 minutes etc. This url with the token sent is normally called capability url.


## How to design a subscription plan?

There are a lot of tricky scenarios with subscription plans, like:

- how to give discounts for a given period of time
- when does the billing period begins (start of month, or the time when the payment is made)
- how does the billing gets charged? (monthly, every exact 30 days?)
- how to refund when the user's terminates early (a simple way is to make sure they pay for 1 month worth of service)

https://github.com/alextanhongpin/subscription

https://www.chargebee.com/blog/recurring-billing-costs/
