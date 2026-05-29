# Lab: Documentation & Platform Alignment (READMEs & Pull Request Generation)

## Scenario

Writing code is only half the battle. In professional environments, maintaining up-to-date documentation and communicating code changes clearly during code reviews are just as critical.

In this lab, you will act as a Lead Developer. You will start with a simple, undocumented Python utility. You will use Copilot to analyze your workspace and generate a production-ready `README.md` from scratch. You will then implement a functional update, save your exact code changes into a `changes.diff` reference file, and instruct Copilot to convert that raw diff file into a highly structured, professional Pull Request description.

---

## Phase 1: Provisioning the Workspace & Setting the Baseline

We need a simple application to start documenting and refactoring. We will use a GitHub Codespace so we have access to a clean workspace and automated git tracking.

1. Navigate to GitHub.com and create a new, empty public repository named `copilot-docs-automation`.
2. Click the green `<>` Code button, select the Codespaces tab, and click **Create codespace on main**. Wait for the environment to provision.
3. In the root of your workspace, create a file named `inventory.py` and paste the following Python script representing a simple inventory tracking system:
   ```python
   import json

   class InventoryManager:
       def __init__(self):
           self.items = {}

       def add_item(self, name: str, quantity: int, price: float):
           if name in self.items:
               self.items[name]['quantity'] += quantity
           else:
               self.items[name] = {'quantity': quantity, 'price': price}

       def get_item(self, name: str) -> dict:
           return self.items.get(name, None)

       def total_value(self) -> float:
           return sum(item['quantity'] * item['price'] for item in self.items.values())
   ```

---

## Phase 2: Generating the Master README

Our codebase currently has zero documentation. We need a clean markdown user guide to show others how our manager operates.

1. Open the GitHub Copilot Chat pane.
2. Submit the following documentation prompt: "@workspace Analyze our repository context and generate a complete, professional, interactive `README.md` file from scratch. It must explain what this tool is, include clear installation/prerequisites guidelines, and show interactive code usage snippets for the `InventoryManager` class based on the logic in `#file:inventory.py`."
3. **Verify and Save**: Copy the generated Markdown text. Create a new file in the root of your workspace named `README.md`, paste the contents inside, and save it.

---

## Phase 3: Implementing a Functional Code Change

Now we need to add a brand-new feature to our inventory manager. We must track this change using our local Git system.

1. Open `inventory.py`.
2. Place your cursor inside the `InventoryManager` class, press `Ctrl + I` (`Cmd + I` on Mac) to open the Inline Chat, and prompt: "Add a new method called `export_to_json` that accepts a filepath string, exports the current `self.items` dictionary to a file formatted with `json.dump`, and prints a success logger message."
3. Click **Accept** to apply the new function.

---

## Phase 4: Staging and Saving the Git Diff

Instead of asking Copilot to guess our changes, we will output our exact workspace edits to a dedicated change file so the AI can read the raw modifications.

1. Open your Integrated Terminal (`Ctrl + `` ` or `Cmd + `` `).
2. Verify that Git detects your code modifications by checking the status:
   ```bash
   git status
   ```
3. We need to save the exact line changes of our functional edit. Run the following shell command to generate and save a diff file:
   ```bash
   git diff > changes.diff
   ```
4. Verify that a new file named `changes.diff` has appeared in your VS Code Explorer. Click on it to inspect the standard Git metadata reflecting your newly added `export_to_json` method.

---

## Phase 5: Generating the PR Documentation from our Diff Reference

Now we will feed our physical diff file directly into Copilot to generate a clean, comprehensive Pull Request description.

1. Open GitHub Copilot Chat.
2. Switch your chat pane to Ask Mode or Agent Mode.
3. Submit the following prompt referencing your diff file: *"Analyze the changes documented in `#file:changes.diff`. Write a highly professional, comprehensive Pull Request description for these edits. Format the response strictly in Markdown with the following sections:
   - **Summary of Changes**: Bullet points detailing exactly what was added.
   - **Architectural Impact**: Explain why this change is safe.
   - **Reviewer Checklist**: Interactive checkboxes (e.g., `[ ] Added unit tests`, `[ ] Updated documentation`) to guide the reviewer."*
4. Review the generated description. Notice how accurately the AI converted raw `+` and `-` unified diff lines into standard English.

---

## Phase 6: Creating a Reusable PR Template

To ensure all future developers follow this high standard of communication, we should store this structure inside our repository as a template.

1. In the VS Code Explorer, click the **New Folder** icon and name it `.github` (ensure you include the period).
2. Inside `.github`, create a file named `pull_request_template.md`.
3. Ask Copilot Chat: "Based on the structure of the Pull Request description you just created, write a generic, reusable Pull Request template for our repository. Use markdown placeholders like `[Describe feature here]` so other developers can easily fill it out."
4. Copy the generated template layout, paste it into `.github/pull_request_template.md`, and save the file.

---

## Phase 7: Environment Cleanup (Crucial for Cost Management)

To ensure your GitHub compute limits are protected:

1. Close the Codespace browser tab.
2. Navigate to `github.com/codespaces` in a new browser tab.
3. Locate your active `copilot-docs-automation` Codespace, click the three dots (`...`), and select **Delete** to conserve your free monthly compute quota.
