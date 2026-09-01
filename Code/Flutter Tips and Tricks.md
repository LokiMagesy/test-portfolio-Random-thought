YouTube:  [# Animated Login Form Using HTML & CSS](https://www.youtube.com/watch?v=1H-vSHVOxoU)
[`Introduction_screen`](https://pub.dev/packages/introduction_screen)
introduction_screen: ^3.1.12

`RichText()`
`Flexible()`
`CircleAvatar()`
https://www.youtube.com/watch?v=5vDq5DXXxss
https://www.youtube.com/watch?v=kj_tldMmu4w&list=PL82uaKJraAIJ55f-MSTqj3JNYUVOIjnMx
List [How to build a Flutter Website](https://www.youtube.com/playlist?list=PLdTodMosi-Bxf___3xPh3_NS-on4dc0sJ)
+ [Building a Website In Flutter - Flutter Web Beginners Tutorial](https://www.youtube.com/watch?v=33kyEzDMTZU&list=PLdTodMosi-Bxf___3xPh3_NS-on4dc0sJ)
+ []()
[💰📱 FREE FULL FLUTTER COURSE • Expense Tracker App #1](https://www.youtube.com/watch?v=yDyg0A4wL9Y)
[Google Frontend Interview With A Frontend Expert](https://www.youtube.com/watch?v=ai1zmNO5Z3E)
https://docs.flutter.dev/perf/best-practices

# RESPONSIVE DESIGN

`final currentWidth = MediaQuery.of(context).size.width;`

`backgroundColor: currentWidth < 600 ? Colors.deepPurple[300] : Colors.green[300]` 

`final Widget mobileBody;`
`final Widget desktopBody;`

```
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth < 600) {
      return mobileBody; // mobile body
    } else {
      return desktopBody; // mobile body + desktop element
    }
  }
)
// Expdanded() Help alot in responsive
```

## Widget `FittedBox`
## Widget show Search
## Widget .adaptive (Android & IOS)
`Slider.adaptive()`
`SwitchListTile.adaptive()`
`Switch.adaptive()`
`Icons.adaptive.share`
`CircularProgressIndicator.adaptive()`
## Widget Hero
tag :

1st page `onTap` on element (with tag and image)
2nd page `Hero` (with tag and image) flutter animated the transition
## Widget Stream Builder & Future Builder

`StreamBuilder`
``` dart
StreamBuilder(
  child: Method,
  initialData: 0,
  builder: (context, snapshot) {
	if (snapshot.connectionState == ConnectionState.waiting) {
	  return const CircularProgressIndicator.adaptive();
	} 
	if (snapshot.hasError) {
	  return const Text('Error')
	} else {
	  return Text(
	    snapshot.data.toString(),
	    style: const TextStyle(fontSize: 40),
	  )
	}
  }
)

```
```
```

`FutureBuilder`
``` dart
FutureBuilder(
  future: getData(),
  builder: (context, snapshot) {
	if (snapshot.connectionState == ConnectionState.waiting) {
	  return const CircularProgressIndicator.adaptive();
	} 
	if (snapshot.hasError) {
	  return const Text(snapshot.error.toString())
	} else {
	  return Text(
	    snapshot.data.toString(),
	    style: const TextStyle(fontSize: 40),
	  )
	}
  }
)
```
## Widget Choice Chip
https://www.youtube.com/watch?v=M9J-JJOuyE0 
https://www.youtube.com/watch?v=G1LGOH424lo 
## Widget 
## Widget 
## Widget 
## Widget 
## Widget 
## Widget 
## Widget 

