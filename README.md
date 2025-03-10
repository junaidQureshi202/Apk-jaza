# Apk-jaza
import 'package:flutter/material.dart';
import 'package:video_player/video_player.dart';

void main() {
  runApp(JazaCCTVApp());
}

class JazaCCTVApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Jaza CCTV',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: CCTVLoginScreen(),
    );
  }
}

class CCTVLoginScreen extends StatefulWidget {
  @override
  _CCTVLoginScreenState createState() => _CCTVLoginScreenState();
}

class _CCTVLoginScreenState extends State<CCTVLoginScreen> {
  final TextEditingController _serialController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();

  void _login() {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => LiveCameraScreen(serial: _serialController.text)),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Jaza CCTV Login')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _serialController,
              decoration: InputDecoration(labelText: 'Enter DVR Serial Number'),
            ),
            TextField(
              controller: _passwordController,
              decoration: InputDecoration(labelText: 'Enter Password', obscureText: true),
            ),
            SizedBox(height: 20),
            ElevatedButton(onPressed: _login, child: Text('Login & View Live')),
            SizedBox(height: 20),
            Text('Contact: jazasystems360@gmail.com'),
            Text('Phone: 0315 2061 574'),
          ],
        ),
      ),
    );
  }
}

class LiveCameraScreen extends StatefulWidget {
  final String serial;
  LiveCameraScreen({required this.serial});

  @override
  _LiveCameraScreenState createState() => _LiveCameraScreenState();
}

class _LiveCameraScreenState extends State<LiveCameraScreen> {
  VideoPlayerController? _controller;
  String get rtspUrl => 'rtsp://admin:password@${widget.serial}/Streaming/Channels/101';

  @override
  void initState() {
    super.initState();
    _controller = VideoPlayerController.network(rtspUrl)
      ..initialize().then((_) {
        setState(() {});
        _controller!.play();
      });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Live Camera View')),
      body: Center(
        child: _controller != null && _controller!.value.isInitialized
            ? AspectRatio(
                aspectRatio: _controller!.value.aspectRatio,
                child: VideoPlayer(_controller!),
              )
            : CircularProgressIndicator(),
      ),
    );
  }

  @override
  void dispose() {
    _controller?.dispose();
    super.dispose();
  }
}
