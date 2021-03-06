---
title: Create lists with different types of items
description: How to implement a list that contains different types of assets.
prev:
  title: Create a grid list
  path: /docs/cookbook/lists/grid-lists
next:
  title: Place a floating app bar above a list
  path: /docs/cookbook/lists/floating-app-bar
js:
  - defer: true
    url: https://dartpad.dev/inject_embed.dart.js
---

You might need to create lists that display different types of content.
For example, you might be working on a list that shows a heading
followed by a few items related to the heading, followed by another heading,
and so on.

Here's how you can create such a structure with Flutter:

  1. Create a data source with different types of items.
  2. Convert the data source into a list of widgets.

## 1. Create a data source with different types of item

### Types of items

To represent different types of items in a list, define
a class for each type of item.

In this example, create an app that shows a header followed by five
messages. Therefore, create three classes: `ListItem`, `HeadingItem`,
and `MessageItem`.

<!-- skip -->
```dart
// The base class for the different types of items the list can contain.
abstract class ListItem {}

// A ListItem that contains data to display a heading.
class HeadingItem implements ListItem {
  final String heading;

  HeadingItem(this.heading);
}

// A ListItem that contains data to display a message.
class MessageItem implements ListItem {
  final String sender;
  final String body;

  MessageItem(this.sender, this.body);
}
```

### Create a list of items

Most of the time, you would fetch data from the internet or a local
database and convert that data into a list of items.

For this example, generate a list of items to work with. The list
contains a header followed by five messages. Each message has one
of 3 types: `ListItem`, `HeadingItem`, or `MessageItem`.

<!-- skip -->
```dart
final items = List<ListItem>.generate(
  1200,
  (i) => i % 6 == 0
      ? HeadingItem("Heading $i")
      : MessageItem("Sender $i", "Message body $i"),
);
```

## 2. Convert the data source into a list of widgets

To convert each item into a widget,
use the [`ListView.builder()`][] constructor.

In general, provide a builder function that checks for what type
of item you're dealing with, and returns the appropriate widget
for that type of item.

This example uses the `is` keyword to check the type of item.
It's fast, and automatically casts each item to the appropriate type.
However, there are different ways to approach this problem if
you prefer another pattern.

<!-- skip -->
```dart
ListView.builder(
  // Let the ListView know how many items it needs to build.
  itemCount: items.length,
  // Provide a builder function. This is where the magic happens.
  // Convert each item into a widget based on the type of item it is.
  itemBuilder: (context, index) {
    final item = items[index];

    if (item is HeadingItem) {
      return ListTile(
        title: Text(
          item.heading,
          style: Theme.of(context).textTheme.headline,
        ),
      );
    } else if (item is MessageItem) {
      return ListTile(
        title: Text(item.sender),
        subtitle: Text(item.body),
      );
    }
  },
);
```

## Interactive example

```run-dartpad:theme-light:mode-flutter:run-true:width-100%:height-600px:split-60:ga_id-interactive_example
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp(
    items: List<ListItem>.generate(
      1000,
      (i) => i % 6 == 0
          ? HeadingItem("Heading $i")
          : MessageItem("Sender $i", "Message body $i"),
    ),
  ));
}

class MyApp extends StatelessWidget {
  final List<ListItem> items;

  MyApp({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final title = 'Mixed List';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: ListView.builder(
          // Let the ListView know how many items it needs to build.
          itemCount: items.length,
          // Provide a builder function. This is where the magic happens.
          // Convert each item into a widget based on the type of item it is.
          itemBuilder: (context, index) {
            final item = items[index];

            if (item is HeadingItem) {
              return ListTile(
                title: Text(
                  item.heading,
                  style: Theme.of(context).textTheme.headline,
                ),
              );
            } else if (item is MessageItem) {
              return ListTile(
                title: Text(item.sender),
                subtitle: Text(item.body),
              );
            }
          },
        ),
      ),
    );
  }
}

// The base class for the different types of items the list can contain.
abstract class ListItem {}

// A ListItem that contains data to display a heading.
class HeadingItem implements ListItem {
  final String heading;

  HeadingItem(this.heading);
}

// A ListItem that contains data to display a message.
class MessageItem implements ListItem {
  final String sender;
  final String body;

  MessageItem(this.sender, this.body);
}
```

<noscript>
  <img src="/images/cookbook/mixed-list.png" alt="Mixed list demo" class="site-mobile-screenshot" />
</noscript>
 

[`ListView.builder()`]: {{site.api}}/flutter/widgets/ListView/ListView.builder.html
