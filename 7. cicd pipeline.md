# Lab: Automated CI/CD Pipelines (GitHub Actions)

## Scenario

You have built a local application and written a comprehensive suite of unit tests. However, in modern team environments, you cannot rely on developers remembering to run tests locally before pushing code. You need a gatekeeper.

In this lab, you will act as a DevOps Engineer. You will use GitHub Copilot to design and deploy a Continuous Integration (CI) pipeline using GitHub Actions. You will use Copilot's Plan Mode to outline the steps, Agent Mode to build and configure the pipeline, and then push your repository to the cloud to watch GitHub's automated runners execute your testing suite on every single commit.

---

## Phase 1: Provisioning the Workspace & Setting up the Application

We need a simple application with a valid test suite to run in our CI pipeline. We will use a GitHub Codespace so we have access to git pushing permissions.

1. Navigate to GitHub.com and create a new, empty public repository named `copilot-ci-pipeline`.
2. Click the green `<>` Code button, select the Codespaces tab, and click **Create codespace on main**. Wait for the environment to provision.
3. In the root of your workspace, create a file named `app.py` and paste the following simple logic:
   ```python
   def add(a: float, b: float) -> float:
       return a + b

   def divide(a: float, b: float) -> float:
       if b == 0:
           raise ValueError("Cannot divide by zero")
       return a / b
   ```
4. Create a test file named `test_app.py` in the root of your workspace:
   ```python
   import pytest
   from app import add, divide

   def test_add_success():
       assert add(3, 5) == 8

   def test_divide_success():
       assert divide(10, 2) == 5

   def test_divide_by_zero():
       with pytest.raises(ValueError):
           divide(10, 0)
   ```
5. Create a `requirements.txt` file in your root workspace:
   ```txt
   pytest
   pytest-cov
   flake8
   ```
6. Open the Integrated Terminal (`Ctrl + `` ` or `Cmd + `` `) and verify that your tests pass locally:
   ```bash
   pip install -r requirements.txt
   python -m pytest
   ```

---

## Phase 2: Planning the Workflow (Plan Mode)

GitHub Actions pipelines are written in YAML and stored in a very specific folder path: `.github/workflows/`. Instead of manually looking up the exact syntax for setting up Python and running pytest, we will let Copilot draft a blueprint.

1. Open the GitHub Copilot Chat pane.
2. Switch the chat interface to Plan Mode.
3. Submit the following design prompt: "Plan a GitHub Actions workflow that automates testing for our Python application. It should run on any push to the main branch or any pull request. It needs to spin up a Linux virtual machine, set up Python 3.12, install our dependencies from `requirements.txt`, and run our test suite using pytest."
4. **Review the Blueprint**: Carefully read the step-by-step pipeline outline Copilot generates. Ensure it covers:
   - Event triggers (`on: push`, `on: pull_request`)
   - Virtual machine environment (`runs-on: ubuntu-latest`)
   - Step to check out the repository code
   - Step to configure Python
   - Step to install pip packages
   - Step to execute pytest

---

## Phase 3: Building the Pipeline (Agent Mode)

Now we will transition from planning to execution to let the Agent construct the actual pipeline file.

1. In the same Copilot Chat session, switch the mode to Agent Mode (or click the **Start Implementation** button).
2. Prompt the Agent: "Based on our approved plan, create the complete GitHub Actions workflow file. Save it in the standard directory path at `.github/workflows/test-pipeline.yml`."
3. **Allow File Creation**: Observe Copilot creating the `.github` and `workflows` directories and writing the YAML file. Verify that the file exists and is populated correctly.

---

## Phase 4: Expanding the Pipeline (Adding Linting & Code Coverage)

To make this a true production-ready CI pipeline, we should expand it to check for code quality issues (linting) and upload code coverage artifacts before completing.

1. Keep your Chat in Agent Mode.
2. Submit the following refactoring request: "Let's expand our workflow in `#file:test-pipeline.yml`. Add a linting step right before running tests that uses `flake8` to check for syntax errors or formatting issues. Also, update the pytest step to generate a code coverage report, and add a final step that uploads that coverage report as a build artifact."
3. **Review the changes proposed by Copilot**. It should add:
   - A step running `flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics`
   - A pytest coverage run: `pytest --cov=app --cov-report=xml`
   - The official `actions/upload-artifact` action to store the coverage XML
4. Click **Accept** or **Apply** to save the updated YAML configuration.

---

## Phase 5: Committing, Pushing, and Cloud Verification

Now we will push our changes to GitHub.com. When GitHub detects a commit containing files in `.github/workflows/`, it will automatically trigger the pipeline.

1. Open your Integrated Terminal.
2. Stage, commit, and push your repository changes:
   ```bash
   git add .
   git commit -m "ci: added automated test and linting pipeline"
   git push origin main
   ```
3. **Watch the Execution on GitHub**:
   - Open your web browser and navigate to your repository on GitHub.com (`github.com/your-username/copilot-ci-pipeline`).
   - Click on the **Actions** tab along the top navigation bar.
   - You should see a workflow run in progress (indicated by a yellow spinning circle) with the name of your commit!
   - Click on the active workflow run to watch the logs stream in real-time as the GitHub runner spins up, configures Python, lints your code, runs your tests, and uploads the coverage artifact.
   - Wait until you get a green checkmark indicating a successful pipeline execution!

---

## Phase 6: Environment Cleanup (Crucial for Cost Management)

To ensure your GitHub compute limits are protected:

1. Close the Codespace browser tab.
2. Navigate to `github.com/codespaces` in a new browser tab.
3. Locate your active `copilot-ci-pipeline` Codespace, click the three dots (`...`), and select **Delete** to conserve your free monthly compute quota.
