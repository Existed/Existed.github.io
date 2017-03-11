---
layout: post
title: "[笔记]UITableView &amp; UITableViewController"
date: 2016-12-22 17:41:38 +0800
comments: true
categories: iOS
keywords: iOS, UITableView, UITableViewController
---


  基本的tableView: numberOfRowsInSection:、tableView: cellForRowAtIndexPath:等等就不多说了，主要记录一下UITableView的编辑模式。

假设有一个“Edit”按键用来打开编辑模式，在编辑模式时此案件显示“Done”，为按键添加操作：
```objective-c
  - (IBAction)toggleEditingMode:(id)sender {
      if (_list.count > 0) {
          if ([self.tableView isEditing]) {
              [sender setTitle:@"Edit" forState:UIControlStateNormal];
              [self.tableView setEditing:NO];
          } else {
              [sender setTitle:@"Done" forState:UIControlStateNormal];
              [self.tableView setEditing:YES];
          }
      }
  }
```

<!-- more -->

添加row：
```objective-c
 - (IBAction)addNewItem:(id)sender {

        /* Get a new item */

        [_list insertObject:item atIndex:0];

        NSInteger lastRow = [_list indexOfObject:item];
        NSIndexPath *indexPath = [NSIndexPath indexPathForRow:lastRow inSection:0];

        [self.tableView insertRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationRight];
}
```

{% img /images/UITableView_edit_new.jpeg %}
