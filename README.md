# StayHomeandKeepLearning
Code and resources for building and deploying https://stayhomeandsafe.s3.ap-south-1.amazonaws.com/index.html
Repository containing the code and resources for building and deploying the stayhomeandlearn.org site.

How to setup locally
These are the instructions for setting up the site, including using the Google Sheets API as a CMS and hosting the site on S3 for free (if you are within the limits of the free tier). For that purpose, you'll need to have a Google account, a GCP account, and an AWS account.

In addition, you should have the AWS CLI installed, as it currently uses whatever profile you specify in the aws_config.ini for connecting to AWS.

Setup local repository
Clone repository git clone 
Get into the repository's local folder and create a virtual environment: python3 -m venv venv
Activate the virtual environment: source venv/bin/activate
Install required libraries: pip3 install -r requirements.txt
Using the Google Sheets API
Create a Workbook in Google Sheets
Go to the Google APIs Console
Create a new project.
Click on Enable API and Services. Search for and enable the Google Sheets API.
Go back to the Google APIs Console and click on Enable API and Services again. Now search for and enable the Google Drive API.
Click on Create credentials. For the next 4 questions select: Google Drive API, Web Server (e.g. node.js, Tomcat), Application data, and No, I'm not using them.
Click on What credentials do I need? Select a name for the service account grant it a Project Role of Editor. Select the JSON option for Key type
Download the JSON file.
Copy the JSON file /site_builder and rename it to credentials.json
Find the client_email inside credentials.json. Back in your spreadsheet, click the Share button in the top right, and paste the client email into the People field to give it edit rights. Hit Send.
Setting up an S3 bucket for hosting a static site
Go to the Amazon S3 Console
Create an S3 bucket
Once in the bucket, select Properties and go to the Static website hosting box
Select the option Use this bucket to host a website
Under Index document and Error document put index.html
Save the URL from Endpoint. That's what you'll use for connecting to your site.
Go to Permissions and click Edit
Clear Block all public access, choose Save, and confirm. Make sure that you understand that when you change this anyone on the internet can access the contents of this bucket. That's what you want when you are publishing a site, however don't put anything private there!
Now go to Bucket Policy, replace the bucket name in the following policy, paste it there, and click Save.
{
  "Version":"2012-10-17",
  "Statement":[{
	"Sid":"PublicReadGetObject",
        "Effect":"Allow",
	  "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::BUCKET-NAME-HERE/*"
      ]
    }
  ]
}
This policy will make the objects store in the bucket you just created publicly readable.

Provide AWS configuration
Rename the aws_config_example.ini file to aws_config.ini
Provide profile that you'll use for connecting to AWS
Check region in the Endpoint URL from the previous section (step 6)
If you followed the previous step and created only one S3 bucket put its name after bucket_dev. You can use bucket_prod and cloudfront_id if you have a production version of your site that uses Cloudfront. I use it for stayhomeandlearn.org.
How to use
Building the site
For testing locally
From the root folder of your repository, you can simply run it as follows: python run.py. This will download the sheets from your workbook, generate the site, and open a local version of it in your web browser.

For deploying to dev/prod
You can also build the site and deploy it directly to S3. For the dev bucket you can run the following command: python run.py --env dev.

If you have your production site and Cloudfront configuration setup, you can run python run.py --env prod --clear. This will deploy the site and clear the Cloudfront cache, so that your latest changes on your site get reflected through the CDN.

What if I want use my own HTML/CSS?
If you want to use this to build a different site, go to site_builder/template, add your HTML/CSS code and assets there. Then go to site_builder.py and modify the generate_site function to adapt it to your needs. Build the site when ready.

For these cases, I'd recommend checking Jinja2's documentation.

How to contribute
There's essentially 3 ways of contributing:

Suggest new resources
Submit feedback
Open an issue
