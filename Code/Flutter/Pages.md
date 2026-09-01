1:52:29 / 4:04:41
# WelcomePage

``` dart
List images = [
  "welcome-one.png",
  "welcome-two.png",
  "welcome-three.png",
]

List textsList = [
  ...
]

// underscore is the default context? 
PageView.builder(
	// up and down slider
	scrollDirection: Axis.vertical,
	// The number of image
	itmeCount: images.length,
	itemBuilder: (_, index) {
	return Container(
	  width: double.maxFinite,
	  height: double.maxFinite,
	  decoration: BoxDecoration(
		image: DecorationImage(
		  image: AssetImage(
			"img/"+images[index]
		  ),
		  fit: BoxFit.cover
		)
	  ),
	  child: Container(
		margin: const EdgeInset.only(top: 150, left: 20, right: 20)
		child: Row(
		  mainAxisAlignment: MainAxisAlignment.spaceBetween
		  Children: [
			Column(
			  croxxAxisAlignment: CrossAxisAlignment.start,
			  Children: [
				AppLargeText(text: "Trips"), // textsList[index]
				AppText(text: "Mountain", size: 30),
				SizedBox(height: 20),
				Container(
				  width: 250,
				  child: AppText(
				    text: "Mountain hike give you an incredible sense of freedom along with edurance test",
				    color: AppColors.textColor2,
				    size: 14,
				  ),
				),
				SizedBox(height: 40),
				ResponsiveButton(width: 120,),
			  ]
			),
			Column(
			  children: List.generate(3, (indexDots) {
				  return Container(
				    margin: const EdgeInset.only(bottom: 2),
				    width: 8,
				    height: index==indeDots?25:8,
				    decoration: BoxDecoration(
				      borderRadius: BorderRadius.circular(8),
				      color: index==indeDots ? AppColors.mainColor : AppColors.mainColor.withOpacity(0.3)
				    ),
				  ),
			    }
			  ),
			),
		  ]
		)
	  ),
	);
}),

```

[[AppColor]]
## (Stateless) Widget Text : AppText()
``` dart
double size;
final String text;
final Color color;
({Key? key, 
  this.size=16,
  required this.text,
  this.color=Colors.black54
}) : super(key: key);

return Text(
  text,
  style: TextStyle(
	color: color,
	fontSize: size,
  ),
);

```
## (Stateless) Widget Large Text : AppLargeText()
``` dart
double size;
final String text;
final Color color;
({Key? key, 
  this.size=30, // default value is 30 and dont need required
  required this.text,
  this.color=Colors.black // if give a color it change and the default is black
}) : super(key: key);

return Text(
  text,
  style: TextStyle(
	color: color,
	fontSize: size,
	fontWeight: FontWeight.bold
  ),
);
```

# Button
Stateless
``` dart
bool? isResponsive;
double? width;
ResponsiveButton({
  Key? key,
  this.width,
  this.isResponsive = false
}) : super(key: key);

return Container(
  width: width,
  height: 60,
  decoration: BoxDecoration(
    borderRadius: BorderRadius.circular(10),
    color: AppColors.mainColor
  ),
  child: Row(
    mainAxisAlignment: MainAxisAlignment.center,
    Children: [
      Image.asset("img/button-one.png")
    ]
  ),
);

```

---
# BarItemPage

``` dart
return Container(
  child: Center(
    child: Text("Bar Item Page")
  ),
)
```
# MainPage

``` dart
  List pages = [
    HomePage(),
    BarItemPage(),
    SearchPage(),
    MyPage(),
  ];
int currentIndex=0;
void onTap(int index) {
  setState(() {
    currentIndex = index;
  });
}

return Scaffold(
  backgroundColor: Colors.white,
  Body: pages[currentIndex],
  bottomNaviagtionBar: BottomNavigationBar(
    unselectedFontSize: 0,
    selectedFontSize: 0,
    // type: BottomNavigationBarType.fixed,
    // backgroundColor: Colors.white,
    onTap: onTap,
    currentIndex: currentIndex,
    selectedItemColor: Colors.black54,
    unselectedItemColor: Colors.gray.withOpacity(0.5),
    showUnselectedLabels: false,
    showSelectedLabels: false,
    elevation: 0,
    items: [
      BottomNavigationBarItem(icon: Icon(Icons.apps)),
      BottomNavigationBarItem(icon: Icon(Icons.bar_chart_sharp)),
      BottomNavigationBarItem(icon: Icon(Icons.search)),
      BottomNavigationBarItem(icon: Icon(Icons.person)),
    ]
  ),
);
```
# MyPage

``` dart
return Container(
  child: Center(
    child: Text("My Page")
  ),
)

```
# SearchPage
Stateless
``` dart
return Container(
  child: Center(
    child: Text("Search Page")
  ),
);
```

# HomePage
Stateful
``` dart

class ... extends ... with TickerProviderStateMixin {

var images = {
  "balloning.png":"Balloning",
  "hiking.png": "Hiking",
  "kayaking.png" :"Kayaking",
  "snorkling.png" :"Snorkling",
}

return Scaffold(
// this = context
TabController _tabController = TabController(length: 3, vsync: this);
  body: Column(
    crossAxisAlignment: CrossAxisAlignment.start
    children: [
      // Menu 
	  Container(
	    padding: const EdgeInset.only(top:70, left: 20)
		child: Row(
          children: [
            Icon(Icons.menu, size:30, color:Colors.black54),
            Expanded(child: Container()),
            Container(
              margin: const EdgeInset.only(right:20)
              width: 50,
              height: 50,
              decoration: BoxDecoration(
                borderRadius: BorderRadius.Circular(10),
                color: Colors.grey.withOpacity(0.5),
              )
            )
          ]
        ),
	  ),
	  SizeBox(height: 30),
	  // Discover
	  Padding(
	    margin: const EdgeInset.only(left: 20),
	    child: AppLargeText(text: "Discover"),
	  )
	  SizeBox(height: 20),
	  // Tab bar
	  Container(
	    child: Align(
		  alignment: Alignment.centerleft,
		  child: TabBar(
		    lavelPadding: const EdgeInsets.only(left: 20, right: 20),
	        controller: _tabController,
	        labelColor: Colors.black,
	        unselectedLabelColor: Colors.grey,
	        isScrollable: true,
	        indicatorSize: TabBarIndicatorSize.label,
	        indicator: CircleTabIndicator(color: AppColors.mainColor, radius: 4),
	        tabs: [
		      Tab(text: "Places"),
		      Tab(text: "Inspiration"),
		      Tab(text: "Emotions"),
		    ]
	     )
	    )	    
	  ),
	  Container(
	    padding: const EdgeInsets.only(left: 20)
	    height: 300,
	    width: double.maxFinite.,
	    child: TabBarView(
  	      controller: _tabController,
	      Children: [
		    ListView.builder(
		      itemCount: 3,
		      scrollDirection: Axis.horizontal,
		      itemBuilder: (BuildContext context, int index) {
		        return Container(
		          margin: const EdgeInsets.only(right: 15, top: 10),
		          width: 200, 
		          height: 300,
		          decoration: BoxDecoration(
		            borderRadius: BorderRadius.Circular(20),
	                color: Colors.white,
	                image: DecorationIamge(
	                  image: AssetImage(
	                    "img/mountain.jpeg"
	                  ),
	                  fit: BoxFit.cover,
	                ),
		          ),
		        );
		      }
		    ),
	        Text("hi"),
	        Text("hi"),
	      ]
	    )
	  ),
	  SizeBox(height: 30),
	  Container(
	    margin: const EdgeInsets.only(right: 20, left: 20),
	    child: Row(
	      mainAxisAlignment: MainAxisAlignment.spaceBetween, 
		  children: [
		    AppLargeText(text: "Explore more", size: 22),	      
		    AppText(text: "See All", color: AppColors.textColor1)
		  ]
		)
	  ),
	  SizeBox(height: 10),
	  Container(
	    margin: const EdfeInsets.only(left: 20),
	    height: 120,
	    width: double.maxFinite,
	    child: ListView.builder(
		    itemCount: 4,
		    scrollDirection: Avxis.horizontal,
		    itemBuilder: (_, index) {
	        return Container(
	          margin: const EdgeInsets.only(right:30),
	          child: Column(
	            children: [
	              Container(	              
	                width: 80,
	                height: 80,
	                decoration: BoxDecoration(
	                  borderRadius: BorderRadius.circular(20),
	                  color: Colors.white,
	                  image: DecorationImage(
	                    image: AssetImage('img/${$images.keys.elementat(index)}'),
	                    fit: BoxFit.cover
	                  )
	                )
	              ),
	              SizeBox(height: 10),
	              Container(
	                child: AppText(
	                text: '${$images.values.elementat(index)}', 
	                color: AppColors.textColor2
	                )
	              ),
	            ]
	          )
	        );
	      }	    
	    )
	  )
    ]
  ),
);

indicator: UnderlineTabIndicator(
    borderSide: BorderSide(color: Color(0xDD613896), width: 8.0),
    insets: EdgeInsets.fromLTRB(50.0, 0.0, 50.0, 40.0),
indicator: BoxDecoration(
  border: Border(
    top: BorderSide(
      color: Theme.of(context).accentColor,
      width: 3.0
    ),
  ),
),

// Custom
class CircleTabIndicator extends Decoration {
  final Color color;
  double radius;
  CircleTabIndicator({required this.color, requried this.radius});
  @override
  BoxPainter createBoxPainter([VoidCallback? onChanged]) {
    return _CirclePainter(color: color, radius: radius)
  }
}

class _CirclePainter extends BoxPainter {
  final Color color;
  double radius;
  _CirclePainter({required this.color, requried this.radius});
  @override
  void paint(Canvas canvas, Offset offset, 
	  ImageConfiguration configuration) {
	  Paint _paint = Paint();
	  _paint.color=color;
	  _paint.isAntiAlias=true;

	  final Offset circleOffset=Offset(configuration.size!.width/2 -radius/2, configuration.size!.height-radius)

	 canvas.drawCircle(offset+circleOffset, radius, _paint);
  }
}
```

# Other

``` dart
```
# Main

``` dart
```