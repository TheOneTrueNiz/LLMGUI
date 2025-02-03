from tkinter import scrolledtext
import threading
import time  # For a simple delay in the example LLM response

def get_llm_response(prompt):
    """
    Placeholder function to simulate getting a response from a local LLM.
    **IMPORTANT:** You need to replace this function with your actual LLM interaction code.
    This example just adds a delay and returns a placeholder string.

    For example, if you are using Ollama, you would use the Ollama Python library here
    to send the prompt to your Ollama server and get the response.
    If you are using transformers directly, you would use your model and tokenizer here.
    """
    time.sleep(1)  # Simulate LLM processing time
    return f"**[LLM Placeholder Response]** - You asked: '{prompt}'.  This is where the real LLM response would be."

class LLMChatGUI:
    def __init__(self, master):
        self.master = master
        master.title("Local LLM Chat")

        self.chat_log = scrolledtext.ScrolledText(master, state='disabled', height=20, wrap=tk.WORD)
        self.chat_log.grid(row=0, column=0, columnspan=2, padx=10, pady=10, sticky="nsew")

        self.prompt_entry = tk.Text(master, height=3, wrap=tk.WORD)
        self.prompt_entry.grid(row=1, column=0, padx=10, pady=5, sticky="ew")

        self.send_button = tk.Button(master, text="Send", command=self.send_prompt)
        self.send_button.grid(row=1, column=1, padx=10, pady=5, sticky="e")

        master.grid_rowconfigure(0, weight=1)  # Make chat_log expand vertically
        master.grid_columnconfigure(0, weight=1) # Make prompt_entry expand horizontally

        self.prompt_entry.bind("<Return>", self.send_prompt_event) # Bind Enter key

    def send_prompt_event(self, event):
        self.send_prompt()
        return "break" # Prevent newline in input box on Enter

    def send_prompt(self):
        prompt = self.prompt_entry.get("1.0", tk.END).strip()
        if not prompt:
            return  # Don't send empty prompts

        self.prompt_entry.delete("1.0", tk.END)  # Clear input box
        self.display_message("You: " + prompt + "\n", "user")

        # Call LLM in a separate thread to prevent GUI from freezing
        threading.Thread(target=self.get_and_display_llm_response, args=(prompt,)).start()

    def get_and_display_llm_response(self, prompt):
        llm_response = get_llm_response(prompt) # *** CALL TO YOUR LLM FUNCTION ***
        self.display_message("LLM: " + llm_response + "\n", "llm")

    def display_message(self, message, sender_type):
        self.chat_log.config(state='normal') # Enable editing to append
        if sender_type == "user":
            self.chat_log.tag_config("user_tag", foreground="blue") # Example styling
            self.chat_log.insert(tk.END, message, "user_tag")
        elif sender_type == "llm":
            self.chat_log.tag_config("llm_tag", foreground="green") # Example styling
            self.chat_log.insert(tk.END, message, "llm_tag")
        else:
            self.chat_log.insert(tk.END, message) # Default style
        self.chat_log.config(state='disabled') # Disable editing again
        self.chat_log.see(tk.END) # Scroll to the bottom

if __name__ == "__main__":
    root = tk.Tk()
    gui = LLMChatGUI(root)
    root.mainloop()  
