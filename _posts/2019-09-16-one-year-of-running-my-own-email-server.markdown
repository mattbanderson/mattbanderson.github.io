---

title: One Year of Running My Own Email Server
date: '2019-09-16 18:45:00'
---

# Farewell My Sweet Inbox Prince

I _loved_ Inbox by Gmail. The ease of setting reminders about emails to get them out my inbox, the categorization and grouping of messages, the shortcuts, etc. I migrated to it from regular Gmail as soon as it was available and never looked back. That is, until I learned that Google was going to deprecate Inbox and migrate all users back to Gmail. Granted, many of Inbox's features had been ported to Gmail. But having been through the deprecation of Google Reader and constantly hearing about Hangouts going away, I was tired of Google killing off services I used and decided it was time to take matters into my own hands. Inspired by a thread on [Hacker News](https://news.ycombinator.com) (which one, I do not recall), I decided to try running my own email server. Worst case scenario, I come crawling back to Gmail in a few weeks with a newfound apprecation for the complexities of email.

# The "E" in Email Actually Stands for Easy

Not really, I just made that up. But as it turns out, email is not actually that hard. SMTP is a robust, cross-platform protocol. Conceived in a time when it was not assumed that every computer or device was online all the time, it is remarkably resilient to temporary failures and downtime.

## Infrastructure

Since I am lazy, I was not going to commit to running my hardware. Cloud servers are cheap, and free Azure credits are even cheaper. I spun up a simple VM with the following configuration:

* Ubuntu LTS 18.04
* 1 CPU
* 1 GB RAM 
* Standard HDD
* SSH access via port 22
* Open ports for email as needed (e.g. 25, 110, 143, 465, 587, 993, and/or 995)
* Restrict SSH connections to my IP address 

This setup costs approximately $10/month.

## Get Your Own Domain

If you are going to run your email, you will need to register a domain to use for it, assuming you do not already have one. In the event you decide to go back to hosted email, using a custom domain will avoid ever having to change email addresses again in the future, as most hosting providers support custom domains (sometimes via a paid option). 

You can always set up rules on your old hosting provider to forward emails to your new address to ensure no emails are lost. Eventually people will start using the new domain as that is what they will be seeing in their inboxes.

I use [Namecheap](https://www.namecheap.com/) and [Cloudflare](https://www.cloudflare.com), but any DNS registrar should work. 

The DNS records should look something like this:

| Type | Name | Value |
| ---- | ---- | ----- |
| A    | mail | mail server IP address |
| MX   | mail | mail.yourdomain.tld |
| TXT  | mail._domainkey | mail server DKIM details |

## Email Server Software

Again, because I am lazy, I was not interested in configuring an entire email software stack myself. Fortunately, I found a recommendation on Hacker News for [docker-mailserver](https://github.com/tomav/docker-mailserver), a mail server that runs as a Docker container via [docker-compose](https://docs.docker.com/compose/).

The instructions in the README are quite good, and there is lots of troubleshooting advice in the [wiki](https://github.com/tomav/docker-mailserver/wiki). It has options to fight spam, run antivirus, support SSL via [Let's Encrypt](https://github.com/tomav/docker-mailserver/wiki), and automatic updates to the Docker image itself.

The [Generate DKIM keys](https://github.com/tomav/docker-mailserver#generate-dkim-keys) step in the docker-mailserver README can be used to generate a mail.txt file to be uploaded via your DNS registrar's console/dashboard to set DNS records. 

### Important Environment Variables

The README lists a number of environment variables that can be configured. I edited the following ones for my setup:

```
HOSTNAME=mail
DOMAINNAME=yourdomain.tld
CONTAINER=mail
HOSTNAME_OVERRIDE=mail.yourdomain.tld
RELAY -> use Mailgun settings (see below)
RELAY_PORT=587 (port 25 is blocked on Azure)
SSL_TYPE=letsencrypt
```

### Other Useful Configuration

The `POSTFIX_MESSAGE_SIZE_LIMIT` environment variable can be set to increase the maximum message size limit. This is useful if you need to receive email with large attachments; e.g. photos. NOTE: in my experience with docker-mailserver, this must be set via an environment variable rather than in the postfix configuration in order for the new setting to take effect.

In the dovecot config, it may be useful to increase the `mail_max_userip_connections` value if you find connections to your server from client devices/applications are timing out or being rejected.

If you have issues sending test messages to yourself, remove `$mydomain` from the default the postfix configuration 
`mydestination = $mydomain, localhost.$mydomain, localhost` so that it becomes `mydestination = localhost.$mydomain, localhost` 
per this [StackExchange answer](https://serverfault.com/questions/179419/postfix-recipient-address-rejected-user-unknown-in-local-recipient-table).


## So What's the Catch?

### If It Was _That_ Easy, Spammers Would Do It Too

The first obvious gotcha is realizing receiving emails works just fine but sending them does not. If you think about it, this makes perfect sense. If I can spin up a mail server on any cloud provider in just a few minutes, so could every spammer in the world. As a result, Azure blocks SMTP traffic on port 25: (https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/)

The solution in Azure (and I would guess, for other cloud providers) is to use a well-known SMTP relay service, such as [SendGrid](https://sendgrid.com/) or [Mailgun](https://www.mailgun.com/) to send and receive emails. Since I had been using Mailgun to receive emails from my own domain via Gmail, I already had an account. It is probably a good idea to register with your relay service using a different email address in case anything goes haywire; i.e. so you can still receive emails somewhere if the new setup is broken.

The SMTP relay service should have instructions for how to configure DNS records to work with their service. The records for Mailgun look something like this:

| Type  | Name  | Value |
| ----  | ----  | ----- |
| CNAME | email | mailgun.org |
| MX    | yourdomain.tld  | mx.mailgun.org |
| TXT   | mail  | v=spf1 include:mailgun.org ~all |
| TXT   | mailo._domainkey | Mailgun DKIM detail  |

### The "From" Address Looks Ugly

If your "From" address looks odd or excessively lengthy (e.g. `sent by me@mail.yourdomain.tld on behalf of me@yourdomain.tld` or something to that effect) in some mail clients, override the postfix `masquerade_domains` setting to rewrite the sender prior to sending email. In `postfix/main.cf`, set `masquerade_domains = mail.yourdomain.tld yourdomain.tld` to send email as `yourdomain.tld` instead of `mail.yourdomain.tld`.

## Configuring SSL

[Let's Encrypt](https://letsencrypt.org) provides free digital certificates to help encrypt the web. Docker-mailserver uses Let's Encrypt's certbot utility to configure SSL for you email server--just follow the steps in the [wiki](https://github.com/tomav/docker-mailserver/wiki/Configure-SSL).

The certificate is good for 90 days, and you should receive a reminder email from Let's Encrypt 20 days before it expires notifying you that it needs to be renewed. The wiki also has instructions for certificate renewal. One item that is not explicitly mentioned is the Docker container needs to be restarted after the cert is renewed. It is also possible to set up a cron job to automatically update the certificate.

## Miscellaneous Troubleshooting

Occasionally, I have noticed a hiccup with the mail server, which has easily been resolved by SSH-ing into the Azure VM and restarting the Docker container. I am unsure whether this is a docker-mailserver or Azure VM issue, but it happens so infrequently (maybe once per quarter?) that I have not bothered with any further investigation.

If you have not received any emails in a few hours, head over to your SMTP relay service logs and see if anything it diplays any errors. Mailgun's logs are great for this; errors are easily spotted as red bars in a chart. As mentioned earlier, SMTP is a robust protocol and any unreceived emails are not simply lost. Mailgun has an option to re-send the email, and in my experience, the email immediately appears in my inbox after doing so.

# Closing Thoughts

When I embarked on this experiment a year ago, in the back of my mind I expected to have to give up at some point and return to a major email provider. Surprisingly, that has not been the case. I have been slowly converting the email addresses I use over to my custom domain.

Another nice benefit of having a custom domain is that I can give out any email address I want that ends in my domain. Based on my SMTP relay settings, they are all routed to my inbox. 

At first, I did have a few recipients report that my emails went to their spam folders, all of which were in the first couple months after switching. Using a relay provider and proper DKIM settings should eliminate most of this. I do get a handful of spam emails per week, but nothing that requires more than a minute or two to glance at and then delete.

If you go this route, you will need to choose new mail clients if you were accustomed to using a hosting provider's web mail client. I am using Mozilla's [Thunderbird](https://www.thunderbird.net/) on my desktop, and there are numerous options to choose from for mobile devices.

If this all like too much trouble, take your domain and switch to a hosted provider that supports custom domains. If you are looking for an option outside the Google ecosystem, I recommend [Fastmail](https://www.fastmail.com/).





