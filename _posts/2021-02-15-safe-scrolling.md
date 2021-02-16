---
layout: post
title: Safely Scrolling in UICollectionView and UITableView
---
When working with deeplinks, I wanted to scroll to a section of a TableView instead of just the page. I found an edge condition where that section sometimes did not load, causing a crash. To improve stability, we should not try to scroll if that section isn't yet available. I'll share snippets for both tableview and collectionView



First we can check if the row is valid by checking if an index path is valid for a tableview. 
```swift
extension UITableView {
    func isValidIndexPath(_ indexPath: IndexPath) -> Bool {
    guard indexPath.row >= 0 && indexPath.section >= 0 else { return false }
    
    let isValidSection = indexPath.section < numberOfSections
    
    var isValidRow: Bool {
        let rowCount = numberOfRows(inSection: indexPath.section)
        return indexPath.row < rowCount || indexPath.row == NSNotFound
    }
    return isValidRow && isValidSection
    }
}
```
This checks the current tableview and makes sure both the section and row aren't greater than the current section and row count.

You may notice my using <code>NSNotFound</code>. You can use NSNotFound as when the section has no sections. See more [here]("https://developer.apple.com/documentation/uikit/uitableview/1614997-scrolltorow")


Now let's add a helper to scroll if we have a valid section. 

```swift 
func safelyScrollToRow(at indexPath: IndexPath, at scrollPosition: UITableView.ScrollPosition, animated: Bool) {
        guard isValidIndexPath(indexPath) else { return }
        scrollToRow(at: indexPath, at: scrollPosition, animated: animated)
    }
```


The same logic also works on UICollectionView, with a few small changes

```swift 
extension UICollectionView {
    
    /// Safely scrolls the collection view contents if the index path is currently loaded
    func safelyScrollToItem(at indexPath: IndexPath, at scrollPosition: ScrollPosition, animated: Bool){
        guard isValidIndexPath(indexPath) else { return }
        scrollToItem(at: indexPath, at: scrollPosition, animated: animated)
    }
    
    func isValidIndexPath(_ indexPath: IndexPath) -> Bool {
        guard indexPath.row >= 0 && indexPath.section >= 0 else { return false }
        
        let isValidSection = indexPath.section < numberOfSections
        
        var isValidRow: Bool {
            let itemCount = numberOfItems(inSection: indexPath.section)
            return indexPath.row < itemCount
        }
        return isValidSection && isValidRow
    }
}
```

Happy Scrolling! Using this safe scroll extension, you can improve the stability of your app. Check out the [gist](https://gist.github.com/VidaHasan/51849007321fa07d0039e6c354aa15ed)!