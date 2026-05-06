# Redis intrusion


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


**-----------**

OK, I've also gone full clickbait — careful as I am, how could I ever let my server be hacked?

Here's what happened. Yesterday I was chatting with a friend who runs Redis on a cloud server of his. One day he suddenly noticed that **all the data in the database was gone**, with only one strange key-value pair left. The value looked like a string version of an RSA public key. He thought he'd accidentally wiped the database. Luckily there was no important data, so he didn't think much of it.

After chatting a bit more, I learned he'd been running a fairly old, unmaintained open-source project, with an old version of Redis installed, and that he wasn't very familiar with Linux. I knew right away: his server had been compromised. Thinking there might be many people like my friend who don't take OS permissions, firewall settings, and database protection seriously, I decided to write up a brief look at why this happens and how to prevent it.

> [!NOTE]
> This attack technique no longer works, because newer Redis versions have added protect mode for safety. We can only do a simple local simulation — don't go testing it for real.

### How it happened

This kind of attack actually goes back to 2015. Back then Redis's security mechanisms were weak and you had to rely on ops to configure things sensibly to keep the database safe. For a while, tens of thousands of Redis nodes worldwide were attacked and exhibited the strange behavior above: all data wiped, with only one key called `crackit` left, whose value resembled an RSA public-key string.

A later investigation revealed that attackers exploited Redis's ability to dynamically set configuration and persist data. They wrote their own RSA public key into the `/root/.ssh/authorized_keys` file on the victim server, allowing them to log in directly as root using their private key.

The compromised servers had very poor security:

1. Redis was on the default port and accessible from the public network.
2. Redis had no password set.
3. The Redis process was started by the root user.

Each one of these alone is risky; together they are deadly. Forget about someone planting their public key — even just connecting and dropping your database is bad enough. So how does the attack actually go? Below is a quick local demo on the loopback address.

### Local demo

Redis listens on port 6379 by default. We'll have it accept connections on 127.0.0.1, so I can definitely connect locally — simulating "Redis is reachable from the public network".

I'm currently a regular user named `fdl`. I want to log in as root via ssh, but I'd need root's password, which I don't know, so I can't.

Apart from password login, ssh also allows RSA key-pair login — but my public key has to be stored in root's home directory at `/root/.ssh/authorized_keys`. We know `/root` doesn't allow access from any other user:

![](https://labuladong.online/algo/images/redis/1.png)

But I find I can connect to Redis directly:

![](https://labuladong.online/algo/images/redis/2.png)

If Redis is running as root, I can use Redis to write my public key into root's home directory. Redis has a persistence mechanism that produces an RDB file containing the raw data.

I smile evilly: first I flush all data from Redis, then write my RSA public key into the database (with newlines at the start and end, to avoid the RDB writer corrupting the public key string):

![](https://labuladong.online/algo/images/redis/3.png)

Now tell Redis to save its data file as `authorized_keys` inside `/root/.ssh/`:

![](https://labuladong.online/algo/images/redis/4.png)

Now root's home contains our RSA public key, and we can log in as root via key pair:

![](https://labuladong.online/algo/images/redis/5.png)

Let's look at the public key we just wrote into root's home:

![](https://labuladong.online/algo/images/redis/6.png)

The garbled bits are some encoding from the RDB file, but the public key in the middle is preserved intact — and the ssh login program even recognized it through the surrounding noise!

Once you have root, you can do whatever you like...

### Lessons learned

Although this kind of attack basically doesn't work anymore (new Redis versions don't expose to the outside network when no password is set), system security is something everyone should take seriously.

When we tinker with stuff, we use cheap cloud servers, often skip a proper firewall to save effort, leave the database unsecured or use a trivial password like `admin` or `root` — "there's nothing important on it anyway". This is not a good habit.

Our computer systems get more sophisticated by the day. Every mature project is maintained by top-tier people. From a tech perspective, they're nearly impregnable. So the only weak link is the people using them.

You often hear of QQ accounts being stolen. I'm sure thieves aren't breaking into Tencent's database — the QQ owner's security awareness is just bad, they entered their credentials on some phishing site, and that's that. I rarely hear of WeChat being stolen — probably because WeChat de-emphasizes password login in favor of QR-code scan login. That, too, is a security consideration; after all, WeChat has a payment feature.

For tech people, scams like the above are easy to spot — just look at the URL, sniff the network in the browser. But most regular folks really can't tell a phishing site from a real one. I honestly didn't expect that in 2020, people were still searching for this Redis exploit, and people were still getting hit...

So back to using Redis: the official site spells out security recommendations clearly. Briefly:

1. Don't start Redis Server as root, and do set a password — and don't make it too short, or it'll be brute-forced.
2. Configure your server firewall and Redis's `config` file so that Redis is, as much as possible, not exposed to the outside.
3. Use the `rename` feature to disguise dangerous commands like `flushall`, to prevent the database from being wiped and data lost.


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

