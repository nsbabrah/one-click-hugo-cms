---
title: Build And Deploy An Angular Form With Netlify Forms And Edge
date: 2020-10-15T05:07:24.766Z
description: Netlify Forms is a form handling feature that receives submissions
  from HTML forms automatically. In this article, we’ll cover how to use it with
  Angular reactive forms and how to deploy the finished app on Netlify’s hosting
  platform, Netlify Edge.
---
reating the frontend, backend, and deployment workflow of an app takes a lot of work. In instances where your app collects only a limited amount of data submissions from its users, building a whole backend may not seem worth the time and effort. An alternative to developing a complete backend is using Netlify Forms. In this tutorial, I’ll explain how you could use an Angular reactive form with Netlify Forms. Since Netlify Forms only work when deployed on Netlify, I’ll also illustrate how to deploy your app on Netlify Edge.

### The Toolkit

An Angular reactive form is a form that has a structured data model created explicitly within a component class using the [ReactiveFormsModule](https://angular.io/api/forms/ReactiveFormsModule) providers. A form model is created for each input element within the form view. This form model is an instance of the [FormControl](https://angular.io/api/forms/FormControl) class and it keeps track of the value of the form element. The form model is immutable because whenever a change is made to the model the FormControl instance returns a new data model instead of updating the old model. Its immutability makes change detection more efficient and allows data alteration with observable operators. Since form input elements are directly connected to their form models, updates between them are synchronous and do not rely on UI rendering.

Netlify is a platform that allows you to build, deploy, and host sites built with various technologies. Sites built with Angular can be hosted on Netlify. Netlify additionally provides a host of tools that simplify, automate, and augment builds and deployments of these sites. We’re going to use two of its products in this tutorial: Netlify Edge and Netlify Forms.

As described earlier, Netlify Forms is a form handling feature that receives submissions from HTML forms automatically. It does not require any submission processing configuration, like creating APIs, scripts, etc. This feature only works with forms in sites deployed on Netlify. It is enabled by default, further reducing the configuration needed to set up the form submissions. Submission handling is set up during deployment where a site’s HTML files are parsed by Netlify’s build bots.

Netlify Edge is a global application delivery network on which sites and applications are published. It provides features like A/B testing, rollbacks, staging, and phased rollouts. All deployments on Netlify Edge are atomic, meaning a site is only live when all files have been uploaded/updated and changes to the site are ready. Once a site is deployed, it is assigned a subdomain on **netlify.app** when deployed to production. Netlify Edge also supports preview and branch deployments (staging, development, etc.).

Netlify Forms submission-handling works because build bots parse HTML forms on a site during deployment. Client-side Javascript rendered forms like those in compiled Angular sites won’t be found by these bots. So the normal set up for Netlify Forms won’t work with Angular Forms.

However, there is a work-around to this. To get it to receive submissions, a hidden plain HTML form is added to the `index.html` file. This form works with the build bots. When submitting the Angular Form, a post request is made to this hidden form which is then captured by Netlify Forms.

In this article, we will create a reactive form. We’ll also develop a service to make a post request to the hidden HTML form. Lastly, we will deploy the app to Netlify Edge.

### Example

To illustrate how to build the app, we will take an example of a feedback form common on many websites. We will use this form to collect comments/complaints, questions, and suggestions from users of the site along with their name and email. We shall also use it to collect their rating of the site.

#### REQUIREMENTS

To follow along with this tutorial, you will need a Netlify account and the Angular CLI installed. If you do not have the CLI, you can install it using npm.

```bash
<!DOCTYPE html>
<html>
<body>

<h2>My First Web Page</h2>
<p>My First Paragraph.</p>

<p id="demo"></p>

<script>
document.getElementById("demo").innerHTML = 5 + 6;
</script>

</body>
</html> 

```

Copy

If you’ve not signed up for a Netlify account yet, you can create one [here](https://app.netlify.com/signup). Netlify offers sign-up through Github, Gitlab, Bitbucket, or Email. Depending on what deployment method you choose to go with, they may be other requirements. They will be stated under each deployment method.