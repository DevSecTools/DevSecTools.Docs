---
layout: default
title: TLS Error
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

A TLS error indicates a misconfiguration of the secure communication protocol in use by the webhost. Common misconfigurations include:
* Using an expired certificate
* Using a certificate that mismatches the hostname/URL
* Using a certificate that is not issued by a trusted certificate authority (e.g. a self-signed certificate)
* Using a certificate that has been revoked

## Exploitation (Why is this a problem?)

Modern browsers complain loudly to end-users when there are TLS errors as they are indicative of potential attacks on the data integrity or privacy of the communication between the end-user and the host. It's therefore important to quickly resolve any benign misconfigurations to avoid inadvertently conditioning end-users to bypass these warnings. System to system integrations suffer the same difficulties, should any client system disable the TLS validation in order to connect to a host with a TLS misconfiguration, it will be unable to detect a compromised communication channel.

A TLS error may indicate an active man-in-the-middle (MiTM) attack in which an adversary is intercepting communications between the end-user and the host in an attempt to eavesdrop or manipulate the content received by the end-user. The implications of a MiTM attack are far reaching, with total compromise of any data sent over the channel allowing for capture of sensitive data as well as compromise of end-user devices by injecting malicious content such as scripts or active website components.

An adversary will (ideally) not have access to a valid TLS certificate and will thus be forced to use an invalid certificate in their interception, for example:
* An old or previously compromised certificate that has since expired or been revoked
* A certificate that is otherwise valid, but belongs to a different website
* A certificate they have generated themselves

## Remediation (How do I fix it?)

TLS errors are usually resolved by configuring the webhost with a suitable TLS certificate, one that is in the correct validity period, matching the hostname, and from a valid certificate authority.

## Prevention (How do I stop this happening again?)

Many major cloud providers offer integrated and fully managed certificate functionality, this can allow for a 'set and forget' approach to configuring TLS on webhosts.
* Azure app service [managed certificates](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate#create-a-free-managed-certificate)
* AWS certificate manager (ACM) [public certificates](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html)
* Google [managed certificates](https://cloud.google.com/load-balancing/docs/ssl-certificates/google-managed-certs)

Similarly, the ACME protocol allows for automation of the certificate issuance workflow and can be used to generate certificates through certificate authorities such as [Let's Encrypt](https://letsencrypt.org/docs/). This approach can be suitable for traditional self managed infrastructure as well as IaaS with a cloud provider that doesn't offer a managed certificate service of their own.

## Read More

<https://datatracker.ietf.org/doc/html/rfc8555#section-4>

<https://en.wikipedia.org/wiki/Man-in-the-middle_attack>

