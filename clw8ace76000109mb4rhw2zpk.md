---
title: "Developing a Customized Browser Plugin for One Digital Trust: A Case Study"
seoTitle: "Custom Cross-Browser Browser Plugin Development with JavaScript"
seoDescription: "Case study on developing a secure, user-friendly browser plugin for One Digital Trust, enhancing login experiences, and maintaining brand consistency"
datePublished: Sun Jul 16 2023 10:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clw8ace76000109mb4rhw2zpk
slug: developing-a-customized-browser-plugin-for-one-digital-trust-a-case-study
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716611213525/f25b249e-e0df-465a-b324-e96b154163cc.avif
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1716611200927/c4449732-9b04-4fb9-8bf0-e90c18984c75.avif

---

Innotek was recently approached by One Digital Trust (ODT), a pioneer in estate and inheritance planning platforms, with a challenge to develop a robust browser plugin to streamline user login experiences.

# **The Problem Space**

ODT had previously engaged with another agency but found their needs unmet. They required a versatile browser plugin that could store users’ login IDs (either a username or email) for their comprehensive platform. Moreover, the plugin needed to be flexible enough to adapt to the visual branding of their institutional partners, enhancing their collaborative offerings.

## **Security Considerations**

Security was a key issue for ODT; they insisted that the plugin shouldn’t provide a login portal but rather route users to log in through the ODT platform.

## **Identifying Login Forms: A Technical Puzzle**

Our work on the plugin offered fascinating insights into the varied nature of login forms. We managed to easily handle standard forms featuring both user ID and password fields, but faced challenges with forms that revealed the submit button only after some user action or with ones that used animations, complicating form detection after page load.

# **The Innovative Solution**

We rapidly created and launched the initial plugin version on the Chrome Store within a week. For controlled testing, we limited access to a select group of users via tester accounts in Chrome Web Store’s Developer Dashboard.

## **Ensuring Security**

To secure the plugin, we leveraged ODT’s existing backend architecture, allowing activation through their website. Users would install the plugin, activate it by logging in via ODT’s website, and after successful authentication, could enable the plugin from the web app. After the activation each time a user logged into a platform like LinkedIn, Gmail, or Facebook, the plugin would offer to securely save the Login ID to ODT.

## **Navigating the World of Login Forms**

Through smart utilization of DOM lookups, we were able to identify login forms on web pages. On detecting a login form, we attached a listener to the form submit event, captured the Login ID, and allowed the original event to proceed ensuring a seamless user experience.

## **Crafting an Aesthetically Pleasing Experience**

To maintain brand consistency and avoid conflict with page-specific styles, the plugin was designed to appear in an iframe inside the DOM on each page. This way, no matter where our users logged in, they would always recognize the familiar face of ODT.

Our innovative solution to ODT’s unique requirements demonstrates Innotek’s commitment to providing cutting-edge solutions while ensuring utmost security and user-friendly design.

## **Want to work with us?**

Drop an email to [**hello@akoskm.com**](mailto:hello@akoskm.com).