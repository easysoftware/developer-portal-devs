# EasyProject Terminology

*In this article we try to explain the most common business-related words in the company*

---


### B2B

*New RYS, encapsulating whole CRM/business functionality for Easy in cleaner way than an original attempt of CRM plugin with custom fields. There are plans to divide it into 2 parts, one as a plugin for the customers, other for Easy specific needs.*

---

### calDav
*Extension of WebDAV which organizes data (meetings, projects, ...) to collections and it is used for synchronization of calendar from EasyProject to mobile phone, part of Easy calendar.*

---

### CRM
*Customer relationship management .. our plugin for RM, which was used as business records for Easy, but quickly overgrew its origins and purpose and become unmaintainable.*

---

### EASY VUE

*Part of the FE that's written in Vue.js and thus rendered on client. We are gradually switching from rending everything on the server to using a modern JS framework in combination with GraphQL on the back-end. For that we picked the Vue.js framework.
`easy_vue` is a plugin that contains all you need to get it running.*

---

### EP
*EasyProject, same product as EasyRedmine, but configured for different audience.*

---

### ER
*EasyRedmine, same product as EasyProject, but configured for different audience.*

---

### ESko
*It's instance of RM with various plugins for Easy needs. Also act's as a business evidence for the company (eg customers, deals, environment configuration, devops ..).*

---

### Lead
*Information about first contact with the customer (for example he asked for a trial). See **EasyLead** in the source codes.*

---

### Mother (matka)
*Seed of db configuration for trials, eg when new trial is created, it gets copy of mother's database for start.*

---

### Opportunity
*Entity for Sales team to negotiate with customer potential deal, it usually comes from lead . See **EasyCrmCase** in the source codes (it's original CRM plugin entity).*

---


### Quote

*Entity which holds offer of some price for some product&period (defined in a pricebook) . See **EasyPriceBookQuote** in the source codes. Items are in **EasyCrmCaseItem**.*

---

### RM
*Redmine, ancestor of our product, it's open-source.*

---

### RYS
*Plugin for Redmine based on Rails engine, see our documentation.*

---

### Timesheet
*Plugin, adds ability to track spent time on issues.*

---

### Trial
*Full instance of ER/EP for free for limited period of time for customers. All trials run on the same server, they even share instance of application, but there is 1 instance of a db per 1 trial.*
