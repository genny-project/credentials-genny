## Creating a Customer Project 
1. Choose customer code 
- Create a customer code which is the name of the project with no spaces and all lowercase. Ex: channel40, mentormatch.


2. Choose Customer domain name
- Select the domain name such as channel40.com.au, mentormatch.com etc.


3. Create a gmail account
- Create a gmail account following the first accounts format as the convention ${domain}.user@gmail.com such as channel40.com.user@gmail.com,  mentormatch.com.user@gmail.com, etc.


4. Enable Google Sheets API
- Go to [Google Developer Console](https://console.developers.google.com) and in [dashboard](https://console.developers.google.com/apis/library) search for Google Sheets API and click on enable. Then click in the hamburguer icon or API button on the left and navegate to [credentials](https://console.developers.google.com/apis/credentials). click in create credentials and select OAuth Client ID, application type other and type the name of the project.
 
 
5. Create a Github Repository
- Create a repository on your personal or company account and give it a name with the following format. credentials-${customer_code}. Ex: credentials-channel40.


6. Create Environment Variables
- Copy the url of the repository a assign it to CREDENTIALS_URL also create another env variable for the hidden genny folder containing the path.

```
export CREDENTIALS_URL=<Repo URL>
export GENNY_CREDENTIALS=~/.genny/credentials
```

7. Create Hidden Genny Folder
- Create a hidden folder with the name genny on your home directory and a subfoler named credentials.

```
mkdir -p $GENNY_CREDENTIALS
```
8. Clone Repository into  Credentials folder
- Clone the project into Credentials created in step 6 with the following command:

```
export CUSTOMER_CODE=$(git -C $GENNY_CREDENTIALS clone $CREDENTIALS_URL 2>&1 | sed 's/.*\('\''.*'\''\).*/\1/' | tr -d "'" | head -1)
```

9. Download Secret File 
- Download the Secret Client Json file  which usually comes with the name *client_secret.${random numbers}.apps.googleusercontent.com.json* change name, append the output to a new file named conf.env and copy to the credentials folder from step 6 by running this commands

```
mv client_secret.${random numbers}.apps.googleusercontent.com.json* secret.json; 
sed -e 's/\//\\\//g' secret.json | awk '{print "GOOGLE_CLIENT_SECRET="$1}' > $GENNY_CREDENTIALS/$CUSTOMER_CODE/conf.env
```

10. Create SpreadSheets
- Create the spreadsheets with the following structure: Hosting -> Projects -> modules. Refer to Google drive to see the structure in practice. 


11. Assign Spreadsheet Id to GOOGLE_HOSTING_SHEET_ID
- Copy the Spreadsheet Id and assign it to GOOGLE_HOSTING_SHEET_ID env and append it to the conf.env file residing in the credentials-$CUSTOMER_CODE with the following command:

```
export GOOGLE_HOSTING_SHEET_ID=<Sheet_id>
echo GOOGLE_HOSTING_SHEET_ID=$GOOGLE_HOSTING_SHEET_ID >> $GENNY_CREDENTIALS/$CUSTOMER_CODE/conf.env

```

12. Create StoredCredential 
- Remove any StoredCredential that is in your local machine and then go to qwanda-services to run the script get_secret.sh by following these commands:

```
rm ~/.genny/sheets.googleapis.com-java-quickstart/StoredCredential
cd <path to qwanda-services>
./get_secret.sh
cp ~/.genny/sheets.googleapis.com-java-quickstart/StoredCredential $GENNY_CREDENTIALS/$CUSTOMER_CODE
```
13. Update Github Repo 
- Update the project with the current changes and poosh it to Github with the following commands:

```
cd $GENNY_CREDENTIALS/$CUSTOMER_CODE
git add .
git commit -m "credentials and configs completed"
git push 
```

14. Check Your Work 
- After completing all the steps now you can run the run-setup script in genny-main 
and share it with the team.

```
./run-setup.sh -g https://github.com/<Your account name>/credentials-<Customer Code> -s <Customer Code>
./run-setup.sh -p <Customer Code> -r <Customer Code> up
```

