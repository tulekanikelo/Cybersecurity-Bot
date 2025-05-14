# Cybersecurity-Bot
// Import necessary namespaces for input/output, collections, and LINQ methods.
using System;
using System.Collections.Generic;
using System.Linq;

// Define the main program class.
class Program
{
    // Store the user's name globally across interactions.
    static string userName = "";

    // Store the user's cybersecurity interest globally.
    static string userInterest = "";

    // Create a Random object for selecting random responses later.
    static Random random = new Random();

    // Convert a string to lowercase for consistent comparisons.
    static string ToLower(string str)
    {
        return str.ToLower();
    }

    // Check if the input string contains the keyword (case-insensitive).
    static bool Contains(string input, string keyword)
    {
        return ToLower(input).Contains(ToLower(keyword));
    }

    // Detect emotional sentiment in the user's input.
    static string DetectSentiment(string input)
    {
        if (Contains(input, "worried")) return "worried";
        if (Contains(input, "curious")) return "curious";
        if (Contains(input, "frustrated")) return "frustrated";
        if (Contains(input, "Happy")) return "Happy";
        return "";  // Return empty if no known sentiment is found.
    }

    // Respond appropriately to the detected sentiment.
    static string RespondToSentiment(string sentiment)
    {
        switch (sentiment)
        {
            case "worried":
                return "It's completely understandable to feel that way. Let me share some tips to help you stay safe.";
            case "curious":
                return "Great to see your curiosity! Cybersecurity is a very important topic.";
            case "frustrated":
                return "I'm here to help — security can be complex, but we’ll get through it step-by-step.";
            case "Happy":
                return "i'm very glad that you are happy";
            default:
                return "";  // Return empty if no matching sentiment.
        }
    }

    // Dictionary storing cybersecurity advice by topic.
    static Dictionary<string, List<string>> topicResponses = new Dictionary<string, List<string>>()
    {
        ["password"] = new List<string>
        {
            "Use strong, unique passwords for each account. Avoid using personal information.",
            "Consider using a password manager to keep track of your credentials securely.",
            "Change your passwords regularly, especially if you suspect any account compromise."
        },
        ["scam"] = new List<string>
        {
            "Scammers often pretend to be trusted institutions. Always verify before clicking links.",
            "Avoid giving out personal information to unknown sources.",
            "Be skeptical of messages with urgent language requesting immediate action."
        },
        ["privacy"] = new List<string>
        {
            "Review the privacy settings on your social media accounts regularly.",
            "Avoid oversharing personal information online.",
            "Use browsers with privacy features and consider VPNs."
        },
        ["phishing"] = new List<string>
        {
            "Be cautious of emails asking for personal info. They might be phishing attempts.",
            "Check the sender's email address and avoid clicking suspicious links.",
            "Never open attachments from unknown sources."
        }

    };

    // Detect which keywords (topics) are mentioned in user input.
    static List<string> DetectKeywords(string input)
    {
        var found = new List<string>();
        foreach (var key in topicResponses.Keys)
        {
            if (Contains(input, key))
            {
                found.Add(key);  // Add topic keyword if found in input.
            }
        }
        return found;
    }

    // Generate a chatbot response based on the user's input.
    static string GenerateResponse(string input)
    {
        // Convert input to lowercase for consistent checks.
        string lowerInput = ToLower(input);

        // Detect and store user's name if they introduce themselves.
        if (Contains(lowerInput, "my name is"))
        {
            int pos = lowerInput.IndexOf("my name is");
            userName = input.Substring(pos + 10).Trim();  // Extract name.
            return $"Nice to meet you, {userName}!";
        }

        // Detect and store user's interest in a topic.
        if (Contains(lowerInput, "i'm interested in"))
        {
            int pos = lowerInput.IndexOf("i'm interested in");
            userInterest = ToLower(input.Substring(pos + 18).Trim());  // Extract interest topic.
            return $"Great! I'll remember that you're interested in {userInterest}.";
        }

        // Detect sentiment and respond to it.
        string sentiment = DetectSentiment(input);
        if (!string.IsNullOrEmpty(sentiment))
        {
            return RespondToSentiment(sentiment);
        }

        // If the user asks to hear more about their topic of interest.
        if ((Contains(lowerInput, "tell me more") || Contains(lowerInput, "more details")) && !string.IsNullOrEmpty(userInterest))
        {
            if (topicResponses.ContainsKey(userInterest))
            {
                var responses = topicResponses[userInterest];
                // Return a random tip related to user's interest.
                return $"Here's another tip on {userInterest}: {responses[random.Next(responses.Count)]}";
            }
        }

        // If topic keywords are found, respond with tips on those topics.
        var keywords = DetectKeywords(input);
        if (keywords.Count > 0)
        {
            string fullResponse = "";
            foreach (var keyword in keywords)
            {
                var responses = topicResponses[keyword];
                // Add a random tip for each found keyword.
                fullResponse += responses[random.Next(responses.Count)] + " ";
            }
            return fullResponse.Trim();  // Remove trailing space.
        }

        // Default fallback response if no other condition is met.
        return "I'm not sure I understand. Can you try rephrasing?";
    }

    // Main entry point for the program — starts chatbot interaction.
    static void Main(string[] args)
    {
        // Greet the user.
        Console.WriteLine("Cybersecurity Awareness Bot: Hi! Ask me anything about cybersecurity.");

        while (true)  // Continuous loop until the user decides to exit.
        {
            Console.Write("You: ");  // Prompt user for input.
            string userInput = Console.ReadLine();  // Read user input.

            // Exit the chatbot if user types "exit" or "quit".
            if (ToLower(userInput) == "exit" || ToLower(userInput) == "quit")
            {
                Console.WriteLine("Cybersecurity Awareness Bot: Stay safe online! Goodbye.");
                break;  // Exit the loop and end the program.
            }

            // Generate a response and display it.
            string response = GenerateResponse(userInput);
            Console.WriteLine("Cybersecurity Awareness Bot: " + response);
        }
    }
}
