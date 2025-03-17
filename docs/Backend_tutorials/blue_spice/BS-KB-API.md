# BlueSpice & KB

To communicate and integrate with EasyProject app our KnowlegeBase require one Rys which is BlueSpice. 
It is well known that both projects needs testing and API adjustments from time to time as KnowledgeBase works on Wiki.
In this section we will try to describe how to work with those projects when you want to make sure what is going on.

---

## Wiki API

Most of the requests is common for each wiki app. You can find it documented [HERE](https://www.mediawiki.org/w/api.php).
Also each knowledge base which is created by us contains it, so for example you can also check it on Easy KB, [HERE](https://www.es-kb.easysoftware.com/w/api.php).
That part is helpful to understand the response and request made in case something is not working well, or the API was changed.


## Postman Testing

Now let's focus on step-by-step how-to in case you would like to test kb locally.

To test our KnowledgeBase the best way is to use [Postman](https://www.postman.com/) to check the response output or what is happening under the hood.
In this section we will focus on how to setup environment on Postman to be able to make it working.
In a files folder you will find sub-folder called Postman.

You will find there 2 files. 
1. BlueSpice postman environment which contains variables needed to make the API calls working. Lets describe them one by one.

* **kb-url** => It is a address of your KnowledgeBase url. You can take it from Kb deployment.
* **client_id** => This is something you will find in the same place as the URL
* **client_secret** => same as above
* **access_token** => to get that one you need to do a API call. It will be describe later in this article
* **username** => it is an login of the user you want to use (usually the one you are logged as in EasyProject app)

To use this environment you simply need to import this file to Postman.
To do that go to the app and on the left side select tab called Environments.
Then click Import button which is placed on the top right under the top bar menu
When you import BlueSpice environment hover over it and you will see thick icon. Check it and you will set the environment to active state.

2. BlueSpice postman collection which contains collection of API calls that can be useful for you for testing our KB application.
Each of the API call contains few tabs that are important to you to know.
There are:
- Auth tab - most of the API calls will require access_token mentioned previously
- Headers - probably for most of the time it will not be bothering you
- Body - some data (depends on the call) is required to be send in body, this is for example a case for sending client_id or client_secret, so make sure your data is there


Let's describe the most important calls now.

**POST Get access token action**

This action is the one you need to call always to get the user access_token. This one is used to authorize user to KB.
As we mentioned previously the access_token will be used in Auth tab for most of the API calls.

**GET References**

This action is used for checking the articles connected to specific Task/Issue. It should return list of articles assigned to it.
As a parameter it use:
* type: example: issues 
* id: #id of issue

**PUT Add Reference**

This action is used for adding KB article to Issue. In a body of this request you will find exemplary structure that you should keep for a successful connection.
You can also find it below:
```
{
    "references": {
        "entityType": "Issue",
        "entityId": "778",
        "pagename": "Check_%2B_verify_url_encoded_titles"
    }
}
```

**DELETE Reference**

This is pretty self-explanatory. It is used for removing referenced article from the Issue.

**PUT Sync groups**

This request is also pretty important. It correspond to EasyProject button placed in blue_spice/rys_management on the bottom of page,
called **Update Permissions**. It is used to make sure that user permission is up to date. Thanks to this either user has or has not access to knowledge base.

