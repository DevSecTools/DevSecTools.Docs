---
layout: default
title: Dangling DNS
---

# Dangling DNS

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

## Description

A dangling DNS record is a DNS record that resolves to a location that is no longer under your control. Typically these arise as a result of an incomplete decommissioning but there are several ways they can be inadvertently introduced including:
* Deleting a service, but forgetting to delete at least one of the DNS records that pointed to the service.
* Provisioning DNS records *prior* to provisioning the new service that the DNS records resolve to.
* Leaving DNS records behind during service migration or consolidation.

While dangling `CNAME` records are more readily encountered, `NS`, `MX`, `A` and `AAAA` records can also dangle and be potentially abused by an adversary.

## Exploitation (Why is this a problem?)

A dangling DNS record allows an adversary to establish a service on one of your hostnames, this is referred to as a 'subdomain takeover'. For most takeovers (all except `MX`), the adversary has full control over content served by the host, and can therefore register an SSL certificate for the compromised hostname using [HTTP Challenge](https://datatracker.ietf.org/doc/html/rfc8555#section-8.3) verification.

For example, take the following hypothetical `CNAME` record:

`subdomain.example.com -CNAME-> something.azurewebsites.net`

If `something.azurewebsites.net` has been decommissioned, an adversary can register a new azure website under the same hostname. The existing `subdomain.example.com` will now point to the adversary controlled website. `A` records can also be intercepted in the same way, sometimes involving brute forcing cloud service provisioning until the target IP is acquired.

Dangling `MX` records can be intercepted to receive email on the dangling domain, potentially allowing for interception of password reset emails or creation of email accounts to gain access to admin panels. Dangling `NS` records allow an adversary to ultimately create their own DNS records and control the dangling domain and all subdomains of the dangling domain.

Once established, an adversary controlled subdomain can then be pivoted for further attacks.

### Phishing
* Generation of phishing links that are more readily trusted by victims
* Generation of phishing links that pass content-filtering allow-lists, or otherwise are incorrectly considered trusted/safe by content filtering tooling
* Sending phishing emails with a reputable sender domain

### Attacking related web sites
* Cross domain cookie attacks (`subdomain.example.com` is allowed to modify cookies for `example.com`)
* Defeating Content Security Policies for XSS attacks (the compromised subdomain may exist within the allow-list on another site)
* Defeating CORS for CSRF attacks (the compromised subdomain may exist within the allow-list on another site)
* Defeating iframe security headers for clickjacking attacks (the compromised subdomain may exist within the allow-list on another site)

## Remediation (How do I fix it?)

Remove/delete the offending DNS record.

## Prevention (How do I stop this happening again?)

Some DNS services from public cloud providers offer tight integration with their other cloud services and can be configured so that DNS records are intrinsically linked to the target cloud service ensuring that the DNS record is only present while the target cloud service is also present (e.g. Azure has [alias records](https://docs.microsoft.com/en-us/azure/dns/dns-alias)).

Continuous monitoring with automated alerting will help ensure that any inadvertently dangling record is not left dangling long enough to be exploited.

Complementary controls:
* DMARC can restrict the ability of an adversary controlled subdomain to be used to deliver email
* Certificate Transparency Log monitoring can help alert you to unexpected registration of SSL certificates on subdomains

## Read More

<https://developer.mozilla.org/en-US/docs/Web/Security/Subdomain_takeovers>

<https://www.hackerone.com/blog/Guide-Subdomain-Takeovers>

<https://0xpatrik.com/subdomain-takeover-basics/>
