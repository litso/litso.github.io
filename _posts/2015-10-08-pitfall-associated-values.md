---
layout: post
title: Swift Pitfall - Enum Associated Values are Tuples
---

# Background

 * Me: Hey did something change on the API, I can't update account info
 * API Guy: Nope
 * Me: Are you sure because i haven't made any changes to the client for that specific endpoint
 * API Guy: *Checks server logs* – well... the app is sending up the user's password as part of the URL...
 * Me: Wait... wahh??!?

If you are using an abstraction arround your networking code such as the excellent [Moya library](https://github.com/Moya/Moya) you are familiar with defining your API endpoint in a similar fashion to:

{% highlight swift %}
enum ApiEndpoint {
    case SetPassword(accountId: String,
        oldPassword: String,
        newPassword: String)
    // ... other endpoints
}
{% endhighlight %}

When we create the endpoint we configure it with all the data it needs to make the request. The account ID will form part of the URL while the passwords will be sent in form data (and encrypted).

Here is what my first pass implementing the function to build the url looked like:
{% highlight swift %}
func makeBuggyUrl(endpoint: ApiEndpoint) -> String {
    switch endpoint {
    case .SetPassword(let accountId):
        return "/accounts/\(accountId)/update"
    }
}
let bugUrl = makeBuggyUrl(endpoint)
{% endhighlight %}

Can you spot the problem?

Yep... `accountId` is actually a tuple with our password values in it! This is what the URL ends up looking like:

> /accounts/("1", "old secret", "new secret")/update 

No bueno
Lets try that again this time extracting ALL the associated values.

{% highlight swift %}
func makeUrl(endpoint: ApiEndpoint) -> String {
    switch endpoint {
    case let .SetPassword(accountId, _, _):
        return "/accounts/\(accountId)/update"
    }
}
let url = makeUrl(endpoint)
{% endhighlight %}

Now our URL is as expected:

> /accounts/1/update

Be careful extracting associated values! They behave like tuples and not like function arguments. I'm hoping Apple will at least introduce a warning for this in the future.

