                                                          AI Resume Reviewer Using Amazon Bedrock
Overview
The AI Resume Reviewer is a serverless AWS project that automatically analyzes uploaded resumes and generates AI-powered feedback. When a user uploads a resume to Amazon S3, an AWS Lambda function is triggered. The Lambda function reads the resume content, sends it to Amazon Bedrock for analysis, and generates a feedback report containing strengths, missing skills, improvement suggestions, and an overall score.
________________________________________
Project Objective
Build an event-driven AI application that reviews resumes and provides intelligent career guidance using Amazon Bedrock.
________________________________________
AWS Services Used
•	Amazon S3
•	AWS Lambda
•	Amazon Bedrock
•	Amazon CloudWatch
•	AWS IAM
________________________________________
Skills Demonstrated
•	Generative AI on AWS
•	Amazon Bedrock Integration
•	Event-Driven Architecture
•	Serverless Computing
•	IAM Role Management
•	Cloud Monitoring
•	Prompt Engineering
________________________________________
Project Architecture
Resume Upload (PDF/TXT)
│
▼
Amazon S3
│
▼
S3 Event Trigger
│
▼
AWS Lambda Function
│
▼
Amazon Bedrock
│
▼
AI Feedback Generation
│
▼
feedback-report.txt
│
▼
Amazon S3
________________________________________
Prerequisites
Before starting the project, ensure that:
•	AWS Account is available.
•	Access to Amazon Bedrock is approved.
•	Basic knowledge of AWS services.
•	AWS Region supports Bedrock.
________________________________________
Step 1: Create an Amazon S3 Bucket
1.	Sign in to AWS Management Console.
2.	Navigate to Amazon S3.
3.	Click Create Bucket.
Configuration:
Bucket Name:
resume-reviewer-bucket
Region:
ap-south-1 (Mumbai)
Block Public Access:
Enabled
4.	Click Create Bucket.
Purpose:
The bucket stores uploaded resumes and generated feedback reports.
________________________________________
Step 2: Upload a Sample Resume
Create a sample resume file.
Example:
Rushi
Skills:
AWS
Docker
Linux
Terraform
Experience:
Cloud Support Intern
Education:
MSC Computer Science
Save the file as:
resume.txt
Upload the file to the S3 bucket.
Purpose:
This file will be analyzed by Amazon Bedrock.
________________________________________
Step 3: Enable Amazon Bedrock Access
1.	Open Amazon Bedrock Console.
2.	Navigate to Model Access.
3.	Click Manage Model Access.
4.	Request access to:
•	Claude 3 Haiku
•	Claude 3 Sonnet (Optional)
5.	Wait for approval.
Purpose:
Allows Lambda to invoke foundation models for AI analysis.
________________________________________
Step 4: Create IAM Role for Lambda
Navigate to:
IAM → Roles → Create Role
Select:
Trusted Entity:
AWS Service
Use Case:
Lambda
Attach the following policies:
•	AmazonS3ReadOnlyAccess
•	AmazonBedrockFullAccess
•	CloudWatchLogsFullAccess
Role Name:
ResumeReviewerRole
Create the role.
Purpose:
Provides permissions required by Lambda.
________________________________________
Step 5: Create Lambda Function
Navigate to:
AWS Lambda → Create Function
Choose:
Author From Scratch
Configuration:
Function Name:
resume-reviewer
Runtime:
Python 3.12
Execution Role:
Use Existing Role
Role:
ResumeReviewerRole
Click Create Function.
Purpose:
Processes uploaded resumes and interacts with Amazon Bedrock.
________________________________________
Step 6: Configure S3 Trigger
Inside Lambda:
1.	Click Add Trigger.
2.	Select Amazon S3.
Configuration:
Bucket:
resume-reviewer-bucket
Event Type:
Object Created (All)
Enable Trigger.
Save configuration.
Purpose:
Automatically invokes Lambda whenever a resume is uploaded.
________________________________________
Step 7: Implement Lambda Function
Replace the default Lambda code with the following:
import json
import boto3

s3 = boto3.client('s3')
bedrock = boto3.client('bedrock-runtime')

def lambda_handler(event, context):

    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(
        Bucket=bucket,
        Key=key
    )

    resume_text = response['Body'].read().decode('utf-8')

    prompt = f"""
You are a professional career coach.

Analyze the following resume and provide:

1. Strengths
2. Missing Skills
3. Improvement Suggestions
4. Overall Score out of 10

Resume:

{resume_text}
"""

    body = {
        "anthropic_version": "bedrock-2023-05-31",
        "max_tokens": 500,
        "messages": [
            {
                "role": "user",
                "content": prompt
            }
        ]
    }

    response = bedrock.invoke_model(
        modelId="anthropic.claude-3-haiku-20240307-v1:0",
        body=json.dumps(body)
    )

    result = json.loads(
        response['body'].read()
    )

    feedback = result["content"][0]["text"]

    s3.put_object(
        Bucket=bucket,
        Key="feedback-report.txt",
        Body=feedback
    )

    return {
        "statusCode": 200,
        "body": "Feedback Generated Successfully"
    }
Deploy the function.
________________________________________
Step 8: Configure Lambda Timeout
Navigate to:
Configuration → General Configuration → Edit
Update:
Timeout:
1 Minute
Save changes.
Purpose:
Allows sufficient time for Bedrock inference.
________________________________________
Step 9: Test the Solution
Upload:
resume.txt
to the S3 bucket.
Expected Flow:
1.	Resume uploaded.
2.	S3 event generated.
3.	Lambda triggered automatically.
4.	Resume content extracted.
5.	Prompt sent to Bedrock.
6.	AI feedback generated.
7.	feedback-report.txt saved to S3.
________________________________________
Step 10: Verify Output
Open the S3 bucket.
Expected Objects:
resume-reviewer-bucket
├── resume.txt
└── feedback-report.txt
Sample Output:
Resume Score: 7.5/10
Strengths:
•	AWS fundamentals
•	Linux administration
•	Docker knowledge
Missing Skills:
•	Kubernetes
•	CI/CD Pipelines
Suggestions:
•	Add measurable achievements
•	Include cloud projects
•	Highlight certifications
________________________________________
Step 11: Monitor with Amazon CloudWatch
Navigate to:
CloudWatch → Log Groups
Open:
/aws/lambda/resume-reviewer
Verify:
•	Lambda execution logs
•	Bedrock responses
•	Processing time
•	Errors and exceptions
Purpose:
Provides operational monitoring and troubleshooting.
________________________________________
Expected Outcome
Upon uploading a resume:
•	Lambda is automatically triggered.
•	Resume is analyzed using Amazon Bedrock.
•	AI-generated feedback is created.
•	Feedback report is saved to S3.
•	Logs are available in CloudWatch.
________________________________________
Future Enhancements
1.	Support PDF resumes using Amazon Textract.
2.	Store feedback history in DynamoDB.
3.	Email feedback using Amazon SES.
4.	Build a web frontend using React and API Gateway.
5.	Compare resumes against job descriptions.
6.	Generate ATS compatibility scores.
7.	Recommend certifications and learning paths.
________________________________________
Key Learning Outcomes
After completing this project, you will understand:
•	Serverless application development
•	Event-driven AWS architecture
•	Amazon Bedrock integration
•	Generative AI workflows
•	IAM permission management
•	CloudWatch monitoring
•	Prompt engineering fundamentals
________________________________________
Conclusion
The AI Resume Reviewer demonstrates how Generative AI can be integrated with AWS serverless services to automate resume analysis. The project combines Amazon S3, AWS Lambda, Amazon Bedrock, and CloudWatch to create a scalable and intelligent application capable of delivering personalized career feedback.

