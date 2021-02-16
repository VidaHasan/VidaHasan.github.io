---
layout: post
title: Avoid Fatal Crashes When Accessing Collection Elements
tags: ["swift"]
---
The typical way we access elements in a collection like an array is using the subscript operator. 

```swift
let foodInfo = foods[indexPath.row]
```

With this approach, we expose ourselves to some risk of crashing the app with this error.

{: .box-error} 
**Error** fatal error: Index out of range

Before accessing an element of an array using subscript, we should verify the index is within range. A common pattern might be something like this. 

```swift
if indexPath.row < foods.count {
    let foodInfo = foods[indexPath.row]
    ...
}
```

This is safe and effective, but a bit tedious. We can make this better by writing an extension that basically solves this issue for us with an easy to remember interface.
```swift
extension Collection {
    func get(index: Index) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}
```
This extension will return the value for the index if the collection contains the passed in index exists in the collection. Otherwise it will return nil.

Let's see it in action. If our index is out of range, we'll get a nil optional instead of a crash.

```swift
guard foodInfo = foods.get(indexPath.row) else { return }
...
```

Using optional values instead of accessing index elements directly is much safer and can help improve the stability of your app. Check out the [gist]("https://gist.github.com/VidaHasan/b38b7b897594352d66d757be05681e14") :) 

Thanks for reading.


