import os
import sys
from google import genai
from google.genai import types


def main() -> None:
    api_key = os.environ.get("GEMINI_API_KEY")
    if not api_key:
        print("Error: GEMINI_API_KEY environment variable not set.")
        sys.exit(1)

    client = genai.Client(api_key=api_key)

    config = types.GenerateContentConfig(
        system_instruction=(
            "You are a helpful, direct, and concise personal assistant. "
            "Keep answers clear and practical."
        ),
        temperature=0.7,
    )

    # Create a chat session so the model remembers earlier turns
    chat = client.chats.create(
        model="gemini-2.5-flash",
        config=config,
    )

    print("Assistant ready. Type 'exit' or 'quit' to end.\n")

    while True:
        try:
            user_input = input("You: ").strip()
        except (KeyboardInterrupt, EOFError):
            print("\nExiting.")
            break

        if not user_input:
            continue
        if user_input.lower() in {"exit", "quit"}:
            print("Goodbye.")
            break

        try:
            response = chat.send_message(user_input)
            print(f"\nAssistant: {response.text}\n")
        except Exception as exc:
            print(f"\nError: {exc}\n")


if __name__ == "__main__":
    main()
