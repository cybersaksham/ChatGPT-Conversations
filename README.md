# ChatGPT Conversations

This repository contains some quality conversations of me with ChatGPT on various topics

### Contributing

1. Firstly, you need to make a fork of this repository to your account.

2. Then, you have to download a ChatGPT Desktop client to export conversation as markdown.
  - You can go to the repository https://github.com/lencx/ChatGPT to download the desktop app.
  - Or download directly from https://github.com/lencx/ChatGPT/releases/download/v0.11.0/ChatGPT_0.11.0_linux_x86_64.deb for linux.

3. Now, export your conversation as markdown in desktop app

4. Go to https://regex101.com/r/kUW4Ug/1 and paste the markdown text in test string

5. Paste `\n>(.*)\n\n---((.*\n)*?)---` in Regular Expression field.

6. Paste `<details>\n<summary>$1</summary>$2\n</details>\n` in Substitution field.

7. Copy the generated markdown and save it in your repository.

8. Then create a pull request to this repository.
