Okay, here's a well-structured README.md file based on the information from the video and the project's structure. This README is designed to be comprehensive and guide users on how to set up, run, and deploy the project:

# End-to-End Text Summarization Project with Hugging Face Transformers and AWS Deployment

This project implements an end-to-end text summarization application using a fine-tuned Pegasus Transformer model from Hugging Face. It includes a complete pipeline from data ingestion to model deployment on AWS using GitHub Actions for CI/CD.

## Project Structure
content_copy
download
Use code with caution.
Markdown

text-summarizer-project/
├── .github/workflows/ # GitHub Actions workflow (main.yaml)
│ └── main.yaml
├── config/ # Configuration files
│ ├── config.yaml # Main configuration
│ └── params.yaml # Model parameters
├── constants/ # Constants (paths to config and params files)
│ └── init.py
├── artifacts/ # Data and model artifacts (ignored by Git)
├── research/ # Jupyter notebooks for experimentation
│ ├── 01_data_ingestion.ipynb
│ ├── 02_data_validation.ipynb
│ ├── 03_data_transformation.ipynb
│ ├── 04_model_trainer.ipynb
│ └── 05_model_evaluation.ipynb
├── src/ # Source code
│ ├── text_summarizer/ # Main project package
│ │ ├── components/ # Project components
│ │ │ ├── init.py
│ │ │ ├── data_ingestion.py
│ │ │ ├── data_transformation.py
│ │ │ ├── data_validation.py
│ │ │ ├── model_evaluation.py
│ │ │ └── model_trainer.py
│ │ ├── config/ # Configuration manager
│ │ │ ├── init.py
│ │ │ └── configuration.py
│ │ ├── entity/ # Data entities
│ │ │ └── init.py
│ │ ├── pipeline/ # Training and prediction pipelines
│ │ │ ├── init.py
│ │ │ ├── stage_01_data_ingestion.py
│ │ │ ├── stage_02_data_validation.py
│ │ │ ├── stage_03_data_transformation.py
│ │ │ ├── stage_04_model_trainer.py
│ │ │ ├── stage_05_model_evaluation.py
│ │ │ └── prediction.py
│ │ ├── utils/ # Utility functions
│ │ │ ├── init.py
│ │ │ └── common.py
│ │ ├── init.py
│ │ └── logging.py # Custom logger
├── app.py # FastAPI application
├── Dockerfile # Docker configuration
├── main.py # Main pipeline execution script
├── requirements.txt # Project dependencies
├── setup.py # Package setup
├── template.py # Script to generate project structure
├── README.md # Project documentation (this file)
└── LICENSE # Project license

## Project Workflows

1. **Update `config.yaml`:** Modify the configuration file with the necessary paths and settings for each stage of the pipeline.
2. **Update `params.yaml`:** Adjust model-specific parameters like learning rate, batch size, and number of epochs.
3. **Update `entity`:** Define data classes in `src/text_summarizer/entity/__init__.py` that represent the return types of configuration functions.
4. **Update `configuration manager`:** Modify `src/text_summarizer/config/configuration.py` to handle the configuration parameters defined in `config.yaml`.
5. **Update `components`:** Implement the logic for each stage (data ingestion, validation, transformation, model training, and evaluation) in their respective files within the `src/text_summarizer/components/` directory.
6. **Update `pipeline`:** Create the training and prediction pipelines in `src/text_summarizer/pipeline/`.
7. **Update `main.py`:** Orchestrate the execution of the training pipeline.
8. **Update `app.py`:** (Later) Build the FastAPI application for prediction and potentially training.

## How to Run

### Steps:

1. **Clone the repository:**

    ```bash
    git clone <your_repository_url>
    cd text-summarizer-project
    ```

2. **Create a virtual environment and install dependencies:**

    ```bash
    conda create -n text-s python=3.8 -y
    conda activate text-s
    pip install -r requirements.txt
    ```

3. **Set up project structure:**
    ```bash
    python template.py
    ```

4. **Update Configuration and Parameters:**

    *   Modify `config/config.yaml` and `config/params.yaml` with your desired settings.

5. **Run the training pipeline:**
    *   **Option 1 (Modular):**  Execute each stage individually:
    ```bash
    python src/text_summarizer/pipeline/stage_01_data_ingestion.py
    python src/text_summarizer/pipeline/stage_02_data_validation.py
    python src/text_summarizer/pipeline/stage_03_data_transformation.py
    python src/text_summarizer/pipeline/stage_04_model_trainer.py
    python src/text_summarizer/pipeline/stage_05_model_evaluation.py
    ```
    *   **Option 2 (Orchestrated):** Run the entire training pipeline using `main.py`:
    ```bash
    python main.py
    ```

6. **Run the Prediction Web Application**
    *   Modify `app.py`
    *   Run the `app.py` file

    ```bash
    python app.py
    ```

    *   Access the application at `http://localhost:8080`.
    *   Use the `/train` endpoint to trigger training (optional, if you want to retrain through the app).
    *   Use the `/predict` endpoint to get summaries for new text input.
    *   **Note**: Make sure that the `artifacts/model_trainer` folder is present with the trained model and tokenizer files before running predictions.

## CI/CD Deployment on AWS

This project uses GitHub Actions for CI/CD to deploy the application on AWS.

### Deployment Steps:

1. **Log in to AWS Console:** Create an AWS account if you don't have one and log in to the AWS Management Console.

2. **Create an IAM User:**

    *   Go to IAM (Identity and Access Management).
    *   Click "Users" and then "Add user."
    *   Provide a username (e.g., `text-s`).
    *   Select "Attach policies directly."
    *   Add the following policies:
        *   `AmazonEC2ContainerRegistryFullAccess`
        *   `AmazonEC2FullAccess`
    *   Click "Next" and then "Create user."
    *   Go to the user's "Security credentials" tab.
    *   Click "Create access key."
    *   Select "Command Line Interface (CLI)" and acknowledge the recommendation.
    *   Click "Next" and then "Create access key."
    *   **Download the CSV file containing the access key ID and secret access key. Keep this information secure.**

3. **Create an ECR Repository:**

    *   Go to Amazon ECR (Elastic Container Registry).
    *   Click "Get Started" or "Create repository."
    *   Choose "Private" visibility.
    *   Provide a repository name (e.g., `text-s`).
    *   Click "Create repository."
    *   **Copy the repository URI.** You'll need it later.

4. **Launch an EC2 Instance:**

    *   Go to EC2 (Elastic Compute Cloud).
    *   Click "Launch instance."
    *   Provide a name (e.g., `text-s-machine`).
    *   Choose an Amazon Machine Image (AMI) - **Select Ubuntu**.
    *   Choose an instance type (e.g., `t2.xlarge` - at least 16GB RAM recommended). **Important: Choose an instance type that meets your needs and budget. GPU instances are significantly more expensive.**
    *   Create a key pair (e.g., `text-s-key`) and download the `.pem` file (you'll need this if you want to SSH into the instance directly). Select the created key pair.
    *   Under "Network settings," allow HTTPS and HTTP traffic.
    *   Under "Configure storage," allocate at least 30GB of storage.
    *   Click "Launch instance."
    *   Go to "Instances," select your instance, click "Connect," and then click "Connect" again to open a terminal in your browser.

5. **Configure EC2 as a Self-Hosted Runner:**

    *   In your EC2 instance terminal, execute the following commands one by one (replace placeholders with your actual values):
        *   Update package manager:
        ```bash
        sudo apt-get update -y
        sudo apt-get upgrade -y
        ```

        *   Install Docker:

        ```bash
        sudo apt-get install docker.io -y
        sudo usermod -aG docker $USER
        ```

        *   Verify Docker installation:

        ```bash
        docker --version
        ```
        *   Configure GitHub Actions Runner:
        1. Go to your GitHub repository -> **Settings** -> **Actions** -> **Runners** -> **New self-hosted runner**
        2. Select **Linux** as the runner image.
        3. Copy and paste the commands from the GitHub UI into your EC2 instance terminal one at a time.

        * **Note:**
        1. When prompted for the runner group name, press Enter to use the default.
        2. When prompted for the runner name, enter `self-hosted` and press Enter.
        3. Keep other options as default.

    *   Ensure the runner is active and listening for jobs (the last command you executed should have started it). If it's not, run it again from the runner directory.
    *   **Very Important**: Do not close this terminal session on the EC2 instance. If you close this terminal then your self-hosted runner will go offline.

6. **Set up GitHub Secrets:**

    *   In your GitHub repository, go to **Settings** -> **Secrets and variables** -> **Actions** -> **New repository secret**.
    *   Add the following secrets one by one (replace placeholders with your actual values):
        *   `AWS_ACCESS_KEY_ID`: Your AWS access key ID.
        *   `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key.
        *   `AWS_REGION`: Your AWS region (e.g., `us-east-1`).
        *   `AWS_ECR_LOGIN_URI`: The URI of your ECR repository.
        *   `ECR_REPOSITORY_NAME`: The name of your ECR repository (e.g., `text-s`).

7. **Update `main.yaml`:**

    *   In your project, open `.github/workflows/main.yaml`.
    *   Replace placeholders with your actual values:
        *   `AWS_REGION`
        *   `ECR_REPOSITORY_NAME`
        *   `AWS_ECR_LOGIN_URI`
        *   `PROJECT_NAME` (if you changed it from `text-s`)
    *   **For subsequent deployments (after the first one), uncomment the lines that stop and remove the existing Docker container in the `Continuous Deployment` stage.**

8. **Update `README.md`:**

    *   Update the `README.md` file to reflect your specific setup (repository URL, AWS details, etc.).
    *   Save the ECR repository URI in your `README.md` for future reference.

9. **Commit and Push:**

    *   Commit your changes (including `Dockerfile`, `main.yaml`, updated `README.md`, and any other changes).
    *   Push the changes to your GitHub repository:

    ```bash
    git add .
    git commit -m "Add CI/CD with GitHub Actions and AWS Deployment"
    git push origin main
    ```

10. **Monitor Deployment:**

    *   Go to your GitHub repository and click on the "Actions" tab.
    *   You should see your workflow running. Monitor its progress.

11. **Access the Deployed Application:**

    *   Once the deployment is successful, go to your EC2 instance in the AWS console.
    *   Copy the instance's public IPv4 address.
    *   Open a web browser and go to `http://<your_ec2_instance_public_ip>:8080`.
    *   You should see the FastAPI application.

### Stopping the EC2 Instance:

**Important:** To avoid unnecessary charges, stop your EC2 instance when you're not using it.

1. Go to the EC2 console.
2. Select your instance.
3. Click "Instance state" and then "Terminate instance."
4. Confirm termination.
5. **Also, delete the created IAM user and ECR repository if you don't need them anymore to maintain security and avoid any potential charges.**

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

Bappy (YouTube: [YES WITH BAPPY](<your_youtube_channel_link>))

## Acknowledgments

*   Hugging Face for the Transformers library and the Pegasus model.
*   Google for the Pegasus model.
*   AWS for cloud services.
*   FastAPI for the web framework.
*   The creators of the Samsung dataset.
content_copy
download
Use code with caution.

Remember to:

Replace placeholders (like <your_repository_url>, <your_youtube_channel_link>) with your actual information.

Fill in any missing details specific to your setup.

Thoroughly test the deployment to ensure everything is working as expected.

Regularly update the README.md as your project evolves.

This detailed README.md will be very helpful for anyone who wants to understand, use, or contribute to your text summarization project.
