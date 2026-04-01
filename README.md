# GTM Items

A collection of reusable **Google Tag Manager (GTM) Items**. Right now it just contains **Custom HTML** items for tracking form submissions across popular WordPress plugins and embedded platforms.

---

## Overview

This repository provides drop-in GTM listeners that push standardized events into the `dataLayer`, making it easier to track form submissions in:

* GA4
* Google Ads
* Other analytics platforms

This repo is intended for:

* WordPress developers
* Agencies
* GTM implementers
* Marketers working with WordPress form plugins

---

## Included Listeners

Currently included in this repository:

* Contact Form 7 Listener
* HubSpot Form Listener
* Ninja Forms Listener

---

## General Setup in Google Tag Manager

1. Open Google Tag Manager
2. Create a new **Tag**
3. Choose **Custom HTML**
4. Paste in the listener code
5. Set the trigger to **All Pages**
6. Save and publish

These listeners are designed to run sitewide so they can detect form submissions wherever the form appears.

---

## Listeners

### Contact Form 7 Listener

Tracks Contact Form 7 submissions using the native `wpcf7submit` event.

**Event pushed to dataLayer:**

`cf7submission`

#### Code

```html
<script>
document.addEventListener('wpcf7submit', function(event) {
  window.dataLayer.push({
    'event': 'cf7submission',
    'formId': event.detail.contactFormId,
    'response': event.detail.inputs
  });
});
</script>
```

#### DataLayer Variables

| Variable Name      | Description                                          |
| ------------------ | ---------------------------------------------------- |
| `formId`           | Contact Form 7 form ID                               |
| `response`         | Array of all submitted form fields                   |
| `response.0.value` | Example of grabbing a specific submitted field value |

#### Notes

* Fire this listener on **All Pages**
* Useful when you want GTM to track Contact Form 7 submissions without relying on redirects
* The `response` array allows you to pull submitted field values into GTM Data Layer Variables

---

### HubSpot Form Listener

Tracks embedded HubSpot form submissions using the `postMessage` callback emitted by HubSpot.

**Event pushed to dataLayer:**

`hubspot-form-success`

#### Code

```html
<script type="text/javascript">
window.addEventListener("message", function(event) {
  if (event.data.type === 'hsFormCallback' && event.data.eventName === 'onFormSubmitted') {
    window.dataLayer.push({
      'event': 'hubspot-form-success',
      'hs-form-guid': event.data.id
    });
  }
});
</script>
```

#### DataLayer Variables

| Variable Name  | Description       |
| -------------- | ----------------- |
| `hs-form-guid` | HubSpot form GUID |

#### Notes

* Fire this listener on **All Pages**
* Intended for **embedded HubSpot forms**
* Uses HubSpot’s built-in message callback rather than scraping the DOM
* Helpful when tracking HubSpot forms through GTM and passing form identifiers into analytics platforms

---

### Ninja Forms Listener

Tracks Ninja Forms submissions using the `nfFormSubmitResponse` event.

**Event pushed to dataLayer:**

`ninja_form_submission`

#### Code

```html
<script>
jQuery(function($) {
  $(document).on('nfFormSubmitResponse', function(e, r) {
    var formData = r.response.data,
      formId = formData.form_id,
      formName = formData.settings.title,
      submissionId = formData.actions.save.sub_id;

    dataLayer.push({
      event: 'ninja_form_submission',
      form_id: formId,
      form_name: formName,
      form_submission_id: submissionId
    });
  });
});
</script>
```

#### DataLayer Variables

| Variable Name        | Description                 |
| -------------------- | --------------------------- |
| `form_id`            | Ninja Forms form ID         |
| `form_name`          | Ninja Forms form name/title |
| `form_submission_id` | Ninja Forms submission ID   |

#### Notes

* Fire this listener on **All Pages**
* Requires **jQuery**
* Useful for capturing both the form ID and form name in GTM
* Provides a submission ID that can be useful for debugging or deeper tracking workflows

---

## Example GTM Trigger

For each listener, create a **Custom Event Trigger** in GTM using the corresponding event name.

### Example: Contact Form 7

| Setting      | Value           |
| ------------ | --------------- |
| Trigger Type | Custom Event    |
| Event Name   | `cf7submission` |

### Other Event Names

* `hubspot-form-success`
* `ninja_form_submission`

---

## Example Data Layer Variables in GTM

You can create Data Layer Variables in GTM such as:

| GTM Variable Name          | Data Layer Variable Name |
| -------------------------- | ------------------------ |
| DLV - formId               | `formId`                 |
| DLV - response first value | `response.0.value`       |
| DLV - hs form guid         | `hs-form-guid`           |
| DLV - ninja form id        | `form_id`                |
| DLV - ninja form name      | `form_name`              |
| DLV - ninja submission id  | `form_submission_id`     |

---

## Example GA4 Usage

These listeners can be used to trigger GA4 events such as:

* `generate_lead`
* `form_submission`
* custom platform-specific events

Example parameters you may want to pass:

* `form_id`
* `form_name`
* `hs_form_guid`
* `submission_id`

How you structure those parameters depends on your GTM and GA4 naming conventions.

---

## Best Practices

* Test every listener in **GTM Preview Mode**
* Make sure your **Data Layer Variables** are created before using them in tags
* Use consistent event naming across your GTM container where possible
* Be careful not to duplicate form tracking if plugins, themes, or embedded scripts are already sending events
* Validate that the listener fires only when the form is truly submitted successfully

---

## Troubleshooting

### Event is not firing

* Confirm the Custom HTML tag is set to fire on **All Pages**
* Use GTM Preview Mode to check whether the custom event appears
* Confirm the form plugin is actually loading the expected JavaScript events

### Data Layer Variables are undefined

* Double-check the exact variable names in GTM
* Make sure the variable path matches the data structure exactly
* For Contact Form 7 field values, confirm the correct array index is being used

### HubSpot listener is not working

* Verify the form is an embedded HubSpot form
* Confirm the page is receiving the expected HubSpot `postMessage` callback

### Ninja Forms listener is not working

* Confirm jQuery is loaded
* Confirm Ninja Forms is using the expected submission response event on the page

---

## Roadmap

Potential future additions:

* Gravity Forms listener
* WooCommerce event listeners
* Consent Mode utilities
* Standardized cross-platform form event schema
* Additional GTM helper scripts for WordPress

---

## Author

Greg Whitehead
CTO and Senior Web Developer

---

## License

Add the license of your choice for this repository.

Example:

`MIT`

If you'd like, I can also turn this into a slightly more polished GitHub-style version with badges, a table of contents, and a cleaner developer-focused structure.
