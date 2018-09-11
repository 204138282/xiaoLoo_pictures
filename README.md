##  Flutter:
个人网络图片库.

-   下拉刷新    (Widget)    RefreshIndicator
-   上拉加载    (Widget)    ListView.builder(controller: xxx)

```dart
import 'package:flutter/material.dart';
import 'dart:async';

class MyRefresh extends StatefulWidget {
  final String title;
  MyRefresh(this.title);

  MyRefreshState createState() => new MyRefreshState();
}

class MyRefreshState extends State<MyRefresh> {
  List<int> items = List.generate(16, (int i) => i);
  ScrollController _scrollController = new ScrollController();
  bool isLoading = false;

  @override
  void initState() {
    super.initState();
    _scrollController.addListener(() {
      if (_scrollController.position.pixels == _scrollController.position.maxScrollExtent) {
        print('loadMore. . . ');
        _getMoreData();
      }
    });
  }
  Future _getMoreData() async {
    if (!isLoading) {
      List<int> newEntries = await mokeHttpRequest(items.length, items.length + 10);
      setState(() {
        items.addAll(newEntries);
        isLoading = false;
      });
    }
  }
  Future<List<int>> mokeHttpRequest(int from, int to) async {
    return Future.delayed(Duration(seconds: 2), () {
      return List.generate(to - from, (i) => i + from);
    });
  }

  Future <Null> _handleRefresh() async {
    await Future.delayed(Duration(seconds: 5), (){
      print('refresh');
      setState(() {
        items.clear();
        items = List.generate(40, (i) => i);
        return null;              
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: AppBar(
        title: Text(widget.title)
      ),
      body: new RefreshIndicator(
        child: ListView.builder(
          controller: _scrollController,
          itemCount: items.length + 1,
          itemBuilder: (BuildContext context, int index) {
            if (index == items.length) {
              return _buildLoadText();
            } else {
              return Column(
                children: <Widget>[
                  ListTile(
                    title: Text('index-$index'),
                  ),
                  Divider(height: 2.0, color: Colors.grey)
                ]
            );
            }
          },
          padding: const EdgeInsets.all(10.0),
        ),
        onRefresh: _handleRefresh,
        color: Colors.black,
        backgroundColor: Colors.grey,
      )
    );
  }

  Widget _buildLoadText() {
    return Container(
      child: Padding(
        padding: const EdgeInsets.all(18.0),
        child: Center(
          child: Text('加载中. . .'),
        )
      ),
      color: Colors.blue,
    );
  }

  @override
  void dispose() {
    super.dispose();
    _scrollController.dispose();
  }
}
```

