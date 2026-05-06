# Understanding session and cookie



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**

You're probably familiar with cookies — for instance, after logging into a site for a while you're asked to log in again; or, if you're into web scraping, sometimes the site blocks your scraper. All of this is related to cookies. Once you understand how the server handles cookies and sessions, you can explain these phenomena and even bend a few rules to get free use indefinitely. Let me walk you through it.

### 1. Intro to session and cookie

Cookies exist because HTTP is a stateless protocol. In other words, the server can't remember you — every time you refresh the page you might have to log in again. That's clearly unacceptable. Cookies are like the server slapping a label on you, so when you make subsequent requests it can recognize you by that cookie.

Abstractly: **a cookie can be thought of as a "variable" of the form `name=value`, stored in the browser; a session can be thought of as a data structure — usually a "map" (key-value pairs) — stored on the server**.

Note: I said "a" cookie can be thought of as a variable, but the server can set multiple cookies at once. So sometimes people say cookies are "a group" of key-value pairs — that works too.

The server can set cookies via the HTTP `Set-Cookie` header. For example, here's a simple service in Go:

```go
func cookie(w http.ResponseWriter, r *http.Request) {
    // set two cookies
	http.SetCookie(w, &http.Cookie{
		Name:       "name1",
		Value:      "value1",
	})

	http.SetCookie(w, &http.Cookie{
		Name:  "name2",
		Value: "value2",
	})
    // write a string to the page
	fmt.Fprintln(w, "page content")
}
```

When the browser hits the URL, the browser dev tools show the HTTP exchange. You can see the server response issued two `Set-Cookie` directives:

![](https://labuladong.online/algo/images/session/1.png)

After that, the browser's request includes both cookies in the `Cookie` field:

![](https://labuladong.online/algo/images/session/2.png)

**Cookies are really that simple — they're just a label the server sticks on each client (browser) for identification**. HTTP also offers many parameters for cookies — expiration time, restricting a cookie to a certain path, and so on.

But many of today's sites are complex and involve a lot of data exchange. Take a shopping cart on an e-commerce site — large in size and complex in structure. You can't fit all that into a simple cookie mechanism. And cookies live in HTTP headers; even if you could fit it, that would consume a lot of bandwidth.

A session, combined with cookies, solves this. For example, a single cookie holds something like `sessionID=xxxx`. Only this one cookie is sent to the server. The server uses this ID to look up the corresponding session — a data structure containing the user's shopping cart and other detailed info. The server can then render a page customized for that user, neatly solving the user-tracking problem.

**A session is a data structure designed by the site's developer, so it can carry arbitrary data**. As long as the client's cookie sends a unique session ID, the server finds the matching session and recognizes the client.

Of course, since sessions live on the server they consume server resources, so sessions usually have an expiration time. The server periodically checks and deletes expired sessions. If the user comes back later, they may have to log in again. The server creates a new session and sends the session ID back to the client as a cookie.

So what real-world benefit comes from understanding cookies and sessions? **Apart from interviews, here's a sneaky use case — you can freeload off some services**.

Some sites let you try the service for free the first time. After that, you have to log in and pay. And you'll notice the site somehow remembers your computer — unless you switch computers or browsers, you can't freeload again.

Question: you didn't log in during the trial, so how does the server remember you? Obviously, the server set a cookie on your browser and built a corresponding session in the backend tracking your state. Your browser dutifully sends that cookie on every visit. The server checks the session and knows this browser has already had its free trial — it should pay up now, no more freeloading.

So if I stop the browser from sending the cookie and pretend each time to be a brand-new beginner, I can keep freeloading, right? Browsers store cookies as files in various locations (it varies by browser); you can find and delete them. But for Firefox and Chrome there are extensions that let you edit cookies directly. I use one called EditThisCookie on Chrome — here's their site:

![](https://labuladong.online/algo/images/session/3.png)

These extensions can read the cookies for the current page and let you edit or delete them at will. **Of course, freeloading once or twice is fine; please don't do it heavily. If you want to use it regularly, just pay — otherwise the site loses revenue and ends the free trial entirely**.

That's the brief intro to cookies and sessions. Cookies are part of the HTTP protocol and aren't very complex. Sessions are customizable, so let's look more carefully at the code architecture for managing them.

### 2. Implementing sessions

The principle of sessions isn't hard, but the actual implementation has some craft to it. It usually requires three components working together: classes (interfaces) called `Manager`, `Provider`, and `Session`.

![](https://labuladong.online/algo/images/session/4.jpg)

1. The browser requests, via HTTP, a page at `/content`. A handler function on that route receives the request, parses the cookie out of the HTTP header, gets the stored sessionID, and passes that ID to the `Manager`.

2. The `Manager` plays the role of a session manager. It stores some configuration: how long sessions live, the cookie name, and so on. All sessions live inside a `Provider` held by the `Manager`. So `Manager` passes `sid` (the session ID) to `Provider`, asking it to find which session this ID corresponds to.

3. `Provider` is a container — most commonly a hash table mapping each `sid` to its session. Given the `sid` from `Manager`, it finds the corresponding `Session` struct and returns it.

4. `Session` holds the user's specific info. The handler retrieves this info, generates the user's HTML page, and returns it to the client.

You might ask: why such a fuss? Couldn't we just keep a hash table in the handler mapping `sid` → `Session`?

**This is where design craft comes in.** Let's see why we split things into `Manager`, `Provider`, and `Session`.

Start with the lowest layer, `Session`. Since a session is just key-value pairs, why not use a hash table directly — why abstract it as a data structure?

First, because `Session` may store more than a hash table — also auxiliary data like `sid`, access count, expiration time, last access time, and so on. This makes implementing algorithms like LRU and LFU possible.

Second, because sessions can be stored in different places. If you use the language's built-in hash table, session data lives in memory; with lots of data the program can crash easily, and once the program ends, all session data is lost. So you might want different storage backends — a cache database like Redis, or MySQL, etc.

So `Session` provides a layer of abstraction that hides the storage differences and just exposes a generic set of key-value operations:

```go
type Session interface {
    // set a key-value pair
    Set(key, val interface{})
    // get the value for key
    Get(key interface{}) interface{}
    // delete key
    Delete(key interface{})
}
```

Now `Provider`. In the diagram above it's just a hash table mapping `sid` → `Session`, but in practice it's more complex. We periodically delete sessions; besides expiration, we may use other strategies like LRU caching, which means `Provider` internally needs a structure like a hash linked list to store sessions.

> [!TIP]
> For the magic of LRU, see the earlier post [LRU explained](https://labuladong.online/algo/data-structure/lru-cache/).

So `Provider`, as a container, hides the algorithmic details. It uses suitable data structures and algorithms to organize the `sid → Session` mapping and exposes a few methods for CRUD on sessions:

```go
type Provider interface {
    // create and return a session
    SessionCreate(sid string) (Session, error)
    // delete a session
    SessionDestroy(sid string)
    // look up a session
    SessionRead(sid string) (Session, error)
    // update a session
    SessionUpdate(sid string)
    // garbage-collect expired sessions, similar to LRU
    SessionGC(maxLifeTime int64)
}
```

Finally, `Manager`. It delegates most of the actual work to `Session` and `Provider`. `Manager` is mostly a config bag: session lifetime, expiration policy, available session storage backends. `Manager` hides these details so we can flexibly configure the session mechanism.

In summary, the main reason for splitting the session mechanism into multiple parts is decoupling and customization. There are several Go session libraries on GitHub with simple source code worth studying:

https://github.com/alexedwards/scs

https://github.com/astaxie/build-web-application-with-golang





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

