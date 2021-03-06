Basic Amazon SES Integrating
----------------------------

Before integrating SES we need to install postfix and configure it to send emails. Postfix should be configured properly and it should be able to send emails. After this step we need to verify email addresses from which emails will be sent. Emails can be varified from Amazon web service -> SES -> Email Addresses (Identity Management).

Resources for installing postfix and configuring Amazon SES
    1. `Postfix installation guide from ubuntu community <https://help.ubuntu.com/community/Postfix>`__
    2. `Integrating Amazon SES with Postfix <https://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html>`__

DKIM settings
-------------

DomainKeys Identified Mail (DKIM) lets an organization take responsibility for a message that is in transit.  The organization is a handler of the message, either as its originator or as an intermediary. Their reputation is the basis for evaluating whether to trust the message for further handling, such as delivery. Technically DKIM provides a method for validating a domain name identity that is associated with a message through cryptographic authentication. DKIM attaches a new domain name identifier to a message and uses cryptographic techniques to validate authorization for its presence. The identifier is independent of any other identifier in the message, such in the author's From: field.

It is strongly advised to send DKIM signed emails. As it authenticated the sender and guarantees that message was not modified while in transit. Thus, it's spam score will be reduced and it will have higher chances of ending in inbox rather than spam folder.

Please follow `Easy DKIM in Amazon SES <http://docs.aws.amazon.com/ses/latest/DeveloperGuide/easy-dkim.html>`__ to implement DKIM signature in AWS.



references
``````````
1. `DKIM.org <http://www.dkim.org>`__


Configuring verification link
-----------------------------

After a student registers on edX platform, student will need to verify the email address he/she provided. edX will send a verification link to email id provided by student. By default this verfication link will be addressed to localhost. We need to replace localhost by DNS of edX platform. It requires to modify environmental variable in /edx/app/edxapp/lms.env.json, change variable 'SITE_NAME' to DNS of edX platform. Now whenever student registers, he/she will get verification link pointing ot edX platform and not localhost.
