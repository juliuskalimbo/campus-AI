// ================================
// CAMPUS AI APP - FULL FLUTTER CODE
// Julius Kalimbo
// ================================

import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(const CampusAIApp());
}

class CampusAIApp extends StatelessWidget {
  const CampusAIApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Campus AI',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const HomeScreen(),
    );
  }
}

// =====================================
// HOME SCREEN
// =====================================

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {

    return Scaffold(
      appBar: AppBar(
        title: const Text("Campus AI"),
        centerTitle: true,
      ),

      body: Padding(
        padding: const EdgeInsets.all(16),

        child: Column(
          children: [

            // WELCOME CARD
            Card(
              elevation: 5,
              child: Padding(
                padding: const EdgeInsets.all(20),

                child: Column(
                  children: const [

                    Icon(
                      Icons.school,
                      size: 80,
                      color: Colors.blue,
                    ),

                    SizedBox(height: 10),

                    Text(
                      "Welcome to Campus AI",
                      style: TextStyle(
                        fontSize: 24,
                        fontWeight: FontWeight.bold,
                      ),
                    ),

                    SizedBox(height: 10),

                    Text(
                      "Your Smart University Assistant",
                      textAlign: TextAlign.center,
                    ),
                  ],
                ),
              ),
            ),

            const SizedBox(height: 20),

            // AI CHAT
            Card(
              child: ListTile(
                leading: const Icon(
                  Icons.chat,
                  color: Colors.blue,
                ),

                title: const Text("AI Assistant"),

                subtitle: const Text(
                  "Ask anything about campus"
                ),

                trailing: const Icon(Icons.arrow_forward),

                onTap: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (_) => const ChatScreen(),
                    ),
                  );
                },
              ),
            ),

            // TIMETABLE
            Card(
              child: ListTile(
                leading: const Icon(
                  Icons.calendar_month,
                  color: Colors.green,
                ),

                title: const Text("Timetable"),

                subtitle: const Text(
                  "View your class schedule"
                ),
              ),
            ),

            // EVENTS
            Card(
              child: ListTile(
                leading: const Icon(
                  Icons.event,
                  color: Colors.orange,
                ),

                title: const Text("Campus Events"),

                subtitle: const Text(
                  "Upcoming student activities"
                ),
              ),
            ),

            // MENTAL HEALTH
            Card(
              child: ListTile(
                leading: const Icon(
                  Icons.favorite,
                  color: Colors.red,
                ),

                title: const Text("Mental Health"),

                subtitle: const Text(
                  "Student wellness support"
                ),
              ),
            ),

            // GPA
            Card(
              child: ListTile(
                leading: const Icon(
                  Icons.calculate,
                  color: Colors.purple,
                ),

                title: const Text("GPA Calculator"),

                subtitle: const Text(
                  "Calculate your GPA"
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// =====================================
// CHAT SCREEN
// =====================================

class ChatScreen extends StatefulWidget {
  const ChatScreen({super.key});

  @override
  State<ChatScreen> createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {

  final TextEditingController _controller =
      TextEditingController();

  String response = "";

  bool isLoading = false;

  // =====================================
  // SEND MESSAGE TO AI
  // =====================================

  Future<void> askAI() async {

    String message = _controller.text;

    if (message.isEmpty) return;

    setState(() {
      isLoading = true;
    });

    try {

      String aiResponse =
          await OpenAIService.sendMessage(message);

      setState(() {
        response = aiResponse;
      });

    } catch (e) {

      setState(() {
        response = "Error: $e";
      });

    }

    setState(() {
      isLoading = false;
    });

    _controller.clear();
  }

  @override
  Widget build(BuildContext context) {

    return Scaffold(

      appBar: AppBar(
        title: const Text("Campus AI Chat"),
      ),

      body: Padding(
        padding: const EdgeInsets.all(16),

        child: Column(
          children: [

            // TEXTFIELD
            TextField(
              controller: _controller,

              decoration: InputDecoration(
                hintText: "Ask Campus AI...",

                border: OutlineInputBorder(
                  borderRadius:
                      BorderRadius.circular(12),
                ),
              ),
            ),

            const SizedBox(height: 15),

            // BUTTON
            SizedBox(
              width: double.infinity,

              child: ElevatedButton(
                onPressed: askAI,

                child: const Text(
                  "Send",
                  style: TextStyle(fontSize: 18),
                ),
              ),
            ),

            const SizedBox(height: 20),

            // LOADING
            if (isLoading)
              const CircularProgressIndicator(),

            const SizedBox(height: 20),

            // AI RESPONSE
            Expanded(
              child: SingleChildScrollView(
                child: Container(
                  width: double.infinity,

                  padding: const EdgeInsets.all(16),

                  decoration: BoxDecoration(
                    color: Colors.grey.shade200,

                    borderRadius:
                        BorderRadius.circular(12),
                  ),

                  child: Text(
                    response,

                    style: const TextStyle(
                      fontSize: 18,
                    ),
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// =====================================
// OPENAI SERVICE
// =====================================

class OpenAIService {

  // =====================================
  // PUT YOUR OPENAI API KEY HERE
  // =====================================

  static const String apiKey =
      "YOUR_OPENAI_API_KEY";

  static Future<String> sendMessage(
      String message) async {

    final response = await http.post(

      Uri.parse(
        "https://api.openai.com/v1/chat/completions",
      ),

      headers: {

        "Content-Type": "application/json",

        "Authorization":
            "Bearer $apiKey",
      },

      body: jsonEncode({

        "model": "gpt-4o-mini",

        "messages": [

          {
            "role": "system",

            "content":
                "You are Campus AI for NUST Namibia. "
                "Help students with academics, mental health, "
                "assignments, campus events, timetable, "
                "and student support."
          },

          {
            "role": "user",
            "content": message
          }
        ]
      }),
    );

    final data = jsonDecode(response.body);

    return data['choices'][0]
        ['message']['content'];
  }
}# campus-AI
