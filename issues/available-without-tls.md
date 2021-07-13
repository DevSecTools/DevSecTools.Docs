---
layout: default
title: Available without TLS
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

A webhost can be configured to serve content without TLS (i.e. over regular unsecured HTTP). This configuration is possible even if the host is also configured to securely serve content with TLS (i.e. over HTTPS). A host may serve different content over HTTP, compared to what it serves over HTTPS.

## Exploitation (Why is this a problem?)

When a host serves content without TLS, the communications between the end-user and the host are subject to interception by an adversary, including man-in-the-middle (MiTM) attacks. The implications of a MiTM attack are far reaching, with total compromise of any data sent over the channel allowing for capture of sensitive data as well as compromise of end-user devices by injecting malicious content such as scripts or active website components. This interception can impact both regular end-users who are accessing via a browser as well as system to system integrations.

## Remediation (How do I fix it?)

Configure the webhost to redirect from HTTP to HTTPS, or disable HTTP.

## Prevention (How do I stop this happening again?)

HSTS preload can be used to hardcode your domain name into web browsers, enforcing HTTPS on your domain, and all subdomains. Requests to hosts that cannot serve HTTPS responses are blocked by the browser.

## Read More

<https://hstspreload.org/>

