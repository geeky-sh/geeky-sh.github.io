---
layout: post
title: Email Sender Identity Verification
description: This post contains information on how providers validate the identity of the sender
comments: true
---

Ever wondered why some emails go to spam or junk folder? How do providers like Gmail or Outlook identify which emails are spam and which aren't? Are you curious to know what standards and protocols are in place to prevent spam or phishing attack? Are there any general set of rules that email providers follow to authenticate and authorize the identity of the email sender?

This blog post intends to answer and clarify all the above queries. Yes, there are standard methods in place to authenticate the identity of emails. These methods are followed by all the providers and the senders to ensure that emails are authenticated. They are **SPF, DKIM** and **DMARC**.

### Terms
- Sender - This vendor which is responsible for sending the email. Eg. Sendgrid, Mailchimp
- Provider - The vendor which is responsible for holding and managing the user emails. Eg. Gmail, Outlook

## SPF
**SPF** - **Sender Policy Framework** - helps providers to validate the domain using which the email was sent. It provides a way for provider to get the list of all the authenticated servers for the respective domain, so that it can validated against the source data of the email.

To explain the problem better with an example, let's say we create a new domain - `example.com` and we want to start sending emails using that domain (`help@example.com`) to a group of interested users, we would probably used third-party tools like sendgrid or mailchimp. Now, if we are doing this i.e sending an email from sendgrid using the email id - `help@example.com`, how will Email know that sendgrid is the authhorised sender to send emails on behalf of `help.example.com`? Adversely, if an attacker is using the a different domain (similar to sendgrid) to send emails on behalf of `example.com`, Gmail should be able to flag it as spam or reject it completely. How will it do that?

To solve this SPF is implemented. What this does is that the domain - `example.com`, supplies the list of servers which are allowed to send emails. This list is the validated by the provider against the source of the email. This solution is implemented by adding a DNS record for `example.com` for the domain from which the email is sent. And since DNS record can only be added by the owner of the domain, the result of this activity will authenticate the sender.

## DKIM
**DKIM** - **Domain Keys Identified Mail** - provides the ability for domain owner to sign the email using Asymmetric encryption. This is used in validating the identity of the sender. The way this works is that the sender signs that email using the private keys that no one else has. On the receiving end, the provider validates the signature of the email using the public key for that domain. This key is accessible to everyone. If the validation is successful, we can say that the sender is an entity authorised the send emails on behalf of the domain `examople.com`. Again DNS record is used to store and get the public key of the domain. And since DNS record can only be added by the owner of the domain, the result of this activity will authenticate the identity of the sender.

## DMARC
**DMARC** - **Domain-based Message Authentication Reporting and Conformance** - is a policy which lays out methods for provider on what to do after checking the domain's SPF and DKIM records. A domain owner can have no or relaxed or strict policy on what to do after the validation. Eg. A strict policy for both SPF and DKIM means that only if both the validations are successful should the DMARC validation be called as a success. Here are two examples of what the DNS might contain:

```
v=DMARC1; p=quarantine; adkim=s; aspf=s;
v=DMARC1; p=none; rua=mailto:help@aashd.me
```

Here is what the record might look like in the DNS configuration:
![DNS record](/images/dmarc.png)

In order the verifiy whether your domain has a DMARC entry, use this:
[https://dmarcly.com/tools/dmarc-checker](https://dmarcly.com/tools/dmarc-checker)

## Verification
If you want to check for any email, what and how the SPF, DKIM and DMARK check looks like and contain, you can do the following:

For Gmail, go to the settings of the specific email and click on `show original`. You will be directed to a separate page which contains all the detailed information for the checks.

![Gmail](/images/gmail-email-check.png)

For outlook, under the settings of the specific email go to `View -> View message source`. A popup will open containing all the meta details.

![Outlook](/images/outlook-email-check.png)

Search for SPF, DKIM and DMARC terms over there to see the results of the validation checks performed. It would look something like this:

Gmail:
![Gmail Check](/images/gmail-check-res.png)

Outlook:
![Outlook Check](/images/outlook-check-res.png)

## Sender Identity Verification

On sendgrid, before sending the email, you need to authenticate the sender using domain verification. This process adds relevant DNS records which will help perform SPF and DKIM checks. It would look something like this:

![Sendgrid domain check](/images/sendgrid-domain-check.png)

## Conclusion
Next time, if you are configuring emails and find that they are strangely going to Spam or Junk folder, you know that you need to check whether these checks are implemented and verification returns success.

However, there might be cases where even after SPF and DKIM checks pass, the email is still found in the spam folder. This might be because your email content might be potential spam. Services like gmail also use M.L./A.I. to identify spams which you need to keep in mind.

## References
- [Learn about SPF, DKIM and DMARC](https://www.cloudflare.com/learning/email-security/dmarc-dkim-spf/)
- [Implement DMARC in Sendgrid](https://docs.sendgrid.com/ui/sending-email/how-to-implement-dmarc)
- [Gmail spam and authentication](https://docs.sendgrid.com/ui/sending-email/how-to-implement-dmarc)
- [Setup SPF and DKIM](https://dmarcly.com/blog/how-to-set-up-spf-and-dkim-for-sendgrid)
