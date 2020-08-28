---
created: 2020-01-16T23:37:44+01:00
layout: post
title: Division in Flutter
date: 2020-01-16 23:37
category: flutter
tags: [flutter development]
summary: "A useful Flutter package for streamlining widget styles."
---

Today, I watched [this YouTube video][youtube] which is an intro into Division: a Flutter package which allows you to easily style Flutter widgets using a CSS inspired syntax.
It allows you to use the Dart Cascade Notation (`..`) to add style to widgets including things like gesture inputs, Center widget, elevation, and others that would usually require wrapping the widget in a parent widget. It also has animation support, and the ability to clone and modify a style.

I think this is a pre-compiler package too so the code will be converted into standard Dart code, meaning there should be no overhead when using this package.

Let's have a look at some code examples from the video, this can also be found on the written tutorial [here][resocoder].

Standard Flutter code:
```dart
class MessageDisplayClassic extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        child: Padding(
          padding: const EdgeInsets.all(16),
          child: Text(
            'Lorem ipsum dolor sit amet...',
            style: TextStyle(
              color: Colors.white,
              fontSize: 30,
              fontWeight: FontWeight.bold,
              fontStyle: FontStyle.italic,
            ),
          ),
        ),
        width: 350,
        decoration: BoxDecoration(
          color: Colors.green.shade800,
          borderRadius: BorderRadius.circular(8),
        ),
      ),
    );
  }
}
```

Division package code:
```dart
class MessageDisplayDivision extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Parent(
      child: Container(
        child: Txt(
          'Lorem ipsum dolor sit amet...',
          style: TxtStyle()
            ..textColor(Colors.white)
            ..fontSize(30)
            ..bold()
            ..italic()
            ..textAlign.start(),
        ),
      ),
      style: ParentStyle()
        ..alignment.center()
        ..padding(all: 16)
        ..width(350)
        ..background.color(Colors.green.shade800)
        ..borderRadius(all: 8),
    );
  }
}
```

With this example the code using Division is actually longer, however, the style properties are grouped together under the Parent and Container widgets and have a common interface which makes it easier to read than nesting widgets to get the same result. This is more obvious when the widget tree grows to include things like animation and gesture inputs. Another benefit is that the Division style can be extracted to a variable so you can have a global style file that contains everything you need to style your app and then you can just assign the style with `Txt('Hello, world!', style: myCustomStyle)`.

I'll have a go at building some apps with it, to see if it is actually better, once I've finished my course. I just wanted to make a note here to remind me about it, that is after all, the purpose of this site.


[youtube]: https://youtu.be/kcq8AbVyMbk
[resocoder]: https://resocoder.com/2019/11/14/division-style-your-flutter-widgets-without-confusion-flutter-tutorial