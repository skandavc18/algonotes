# Designing a Social Feed (Timeline) Feature



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**



After reading this article, you will not only master the algorithm pattern but also be able to solve the following problem:

| LeetCode | LiKou | Difficulty |
| :----: | :----: | :----: |
| [355. Design Twitter](https://leetcode.com/problems/design-twitter/) | [355. Design Twitter](https://leetcode.cn/problems/design-twitter/) | 🟠 |

**-----------**



> [!NOTE]
> Before reading this article, you should first study:
> 
> - [Linked List Basics](https://labuladong.online/algo/data-structure-basic/linkedlist-basic/)
> - [Hash Map Basics](https://labuladong.online/algo/data-structure-basic/hashmap-basic/)
> - [Binary Heap Basics](https://labuladong.online/algo/data-structure-basic/binary-heap-basic/)

LeetCode 355 "Design Twitter" is interesting in itself and combines the algorithm of merging multiple sorted linked lists with object-oriented (OO) design — very practical. This article walks through it.

As for what part of Twitter has anything to do with algorithms, the problem statement will make that clear.

## 1. Problem Description and Use Case

Twitter is similar to Weibo. We mainly need to implement these APIs:

```java
class Twitter {

    // The user posts a tweet
    public void postTweet(int userId, int tweetId) {}
    
    // Returns the ids of recent tweets from the people the user follows (including themself)
    // Up to 10 tweets, ordered from most recent to oldest
    public List<Integer> getNewsFeed(int userId) {}
    
    // follower follows followee. Create the user if the id does not exist
    public void follow(int followerId, int followeeId) {}
    
    // follower unfollows followee. Do nothing if the id does not exist
    public void unfollow(int followerId, int followeeId) {}
}
```

A concrete example to clarify how the API is used:

```java
Twitter twitter = new Twitter();

twitter.postTweet(1, 5);
// User 1 posted a new tweet (id = 5)

twitter.getNewsFeed(1);
// returns [5], because everyone follows themself

twitter.follow(1, 2);
// User 1 follows user 2

twitter.postTweet(2, 6);
// User 2 posted a new tweet (id = 6)

twitter.getNewsFeed(1);
// returns [6, 5]
// Explanation: user 1 follows themself and user 2, so the most recent tweets from them are returned.
// 6 must come before 5 because 6 was posted more recently

twitter.unfollow(1, 2);
// User 1 unfollows user 2

twitter.getNewsFeed(1);
// returns [5]
```

This scenario is very common in real life. Take WeChat Moments: I just added a celebrity on WeChat, then refresh my Moments feed; their posts appear in my feed, sorted with everyone else's posts by time. Twitter's follow is one-way, while WeChat friendship is essentially mutual following — unless you've been blocked...

Most of these APIs are easy to implement. The hard part is `getNewsFeed`, because the result must be ordered by time, but the user's follow set changes dynamically. What do we do?

**This is where the algorithm comes in**: if each user's tweets are stored in a linked list, where each node holds an article `id` and a timestamp `time` (recording the post time for comparison), and the list is sorted by `time`, then if a user follows `k` users, we can use the merge-k-sorted-lists algorithm to produce a properly sorted list — and `getNewsFeed` is correct!

We'll explain the algorithm shortly. But even with the algorithm in hand, how should we represent `user` and `tweet` in code so the algorithm flows naturally? **This is where simple object-oriented design comes in**. Let's build it step by step.

## 2. Object-Oriented Design

Per the analysis above, we need a `User` class to store user info, and a `Tweet` class to store tweet info that doubles as a linked-list node. Sketch the overall framework first:

```java
class Twitter {
    private static int timestamp = 0;
    private static class Tweet {}
    private static class User {}

    // The API methods
    public void postTweet(int userId, int tweetId) {}
    public List<Integer> getNewsFeed(int userId) {}
    public void follow(int followerId, int followeeId) {}
    public void unfollow(int followerId, int followeeId) {}
}
```

We nest `Tweet` and `User` inside `Twitter` because `Tweet` needs the global `timestamp`, and `User` needs `Tweet` to record posts — so they're inner classes. For clarity below, each class and method is shown separately.

### Implementing the Tweet class

Per the analysis, `Tweet` is straightforward: each instance records its `tweetId`, post time `time`, and a `next` pointer to the next node in the list.

```java
class Tweet {
    private int id;
    private int time;
    private Tweet next;

    // Need to pass in the tweet content (id) and the post time
    public Tweet(int id, int time) {
        this.id = id;
        this.time = time;
        this.next = null;
    }
}
```

![](https://labuladong.online/algo/images/design-twitter/tweet.jpg)

### Implementing the User class

Thinking about real use cases, a user needs `userId`, a follow list, and a list of their tweets. The follow list should be a Hash Set for uniqueness and fast lookup; the tweet list should be a linked list to support sorted merging. A picture:

![](https://labuladong.online/algo/images/design-twitter/user.jpg)

Following OO design principles, "follow", "unfollow", and "post" are behaviors of a User, and the follow list and tweet list live inside the user, so we add `follow`, `unfollow`, and `post` methods to `User`:

```java
// static int timestamp = 0
class User {
    private int id;
    public Set<Integer> followed;
    // Head node of the user's tweet linked list
    public Tweet head;

    public User(int userId) {
        followed = new HashSet<>();
        this.id = userId;
        this.head = null;
        // Follow yourself
        follow(id);
    }

    public void follow(int userId) {
        followed.add(userId);
    }

    public void unfollow(int userId) {
        // Cannot unfollow yourself
        if (userId != this.id)
            followed.remove(userId);
    }

    public void post(int tweetId) {
        Tweet twt = new Tweet(tweetId, timestamp);
        timestamp++;
        // Insert the new tweet at the head of the list
        // The earlier in the list, the larger the time value
        twt.next = head;
        head = twt;
    }
}
```

### Implementing the API methods

```java
class Twitter {
    private static int timestamp = 0;
    private static class Tweet {...}
    private static class User {...}

    // We need a map from userId to User object
    private HashMap<Integer, User> userMap = new HashMap<>();

    // user posts a tweet
    public void postTweet(int userId, int tweetId) {
        // Create the user if not present
        if (!userMap.containsKey(userId))
            userMap.put(userId, new User(userId));
        User u = userMap.get(userId);
        u.post(tweetId);
    }
    
    // follower follows followee
    public void follow(int followerId, int followeeId) {
        // Create follower if not present
		if(!userMap.containsKey(followerId)){
			User u = new User(followerId);
			userMap.put(followerId, u);
		}
        // Create followee if not present
		if(!userMap.containsKey(followeeId)){
			User u = new User(followeeId);
			userMap.put(followeeId, u);
		}
		userMap.get(followerId).follow(followeeId);
    }
    
    // follower unfollows followee. Do nothing if id does not exist
    public void unfollow(int followerId, int followeeId) {
        if (userMap.containsKey(followerId)) {
            User flwer = userMap.get(followerId);
            flwer.unfollow(followeeId);
        }
    }

    // Returns up to 10 most recent tweet ids from the people the user follows (including themself),
    // ordered from newest to oldest
    public List<Integer> getNewsFeed(int userId) {
        // Need the algorithm — see below
    }
}
```

## 3. Algorithm Design

Merging k sorted linked lists uses a Priority Queue, the most important application of binary heaps. Think of it as a structure that auto-sorts inserted elements — out-of-order inserts are placed in their correct positions, and elements come out in ascending (or descending) order. See [Implementing a Priority Queue with a Binary Heap](https://labuladong.online/algo/data-structure-basic/binary-heap-implement/).

```python
PriorityQueue pq
# Insert in arbitrary order
for i in {2,4,1,9,6}:
    pq.add(i)
while pq not empty:
    # Each call returns the first (smallest) element
    print(pq.pop())

# Output is sorted: 1,2,4,6,9
```

With this powerful structure, the core feature is easy. Note we configure the priority queue to sort by the `time` attribute **in descending order**, since a larger `time` means more recent and should come first:

```java
class Twitter {
    // Earlier code omitted for brevity...

    public List<Integer> getNewsFeed(int userId) {
        List<Integer> res = new ArrayList<>();
        if (!userMap.containsKey(userId)) return res;
        // Ids of users the current user follows
        Set<Integer> users = userMap.get(userId).followed;
        // Automatically sort by time in descending order; capacity equals number of followed users
        PriorityQueue<Tweet> pq = 
            new PriorityQueue<>(users.size(), (a, b)->(b.time - a.time));

        // First, push the head of every user's tweet list into the queue
        for (int id : users) {
            Tweet twt = userMap.get(id).head;
            if (twt == null) continue;
            pq.add(twt);
        }

        while (!pq.isEmpty()) {
            // 10 results is enough
            if (res.size() == 10) break;
            // Pop the tweet with the largest time (most recent)
            Tweet twt = pq.poll();
            res.add(twt.id);
            // Push the next Tweet of that user into the queue
            if (twt.next != null) 
                pq.add(twt.next);
        }
        return res;
    }
}
```

Here's how it works. The GIF below shows the merge process. Suppose we have three Tweet linked lists sorted by `time` in descending order; we merge them in descending order into `res`. Note that the numbers in the nodes are the `time` attribute, not `id`:

![](https://labuladong.online/algo/images/design-twitter/merge.gif)

That completes a heavily simplified Twitter timeline feature. For more data-structure design problems, see [Classic Data-Structure Design Problems](https://labuladong.online/algo/problem-set/ds-design/).







<hr>
<details class="hint-container details">
<summary><strong>Articles that reference this one</strong></summary>

 - [[Practice] Classic Priority Queue Problems](https://labuladong.online/algo/problem-set/binary-heap/)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**



![](https://labuladong.online/algo/images/souyisou2.png)
