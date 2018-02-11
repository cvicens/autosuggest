# gcloud cheat sheet

List active configuration
```
$ gcloud config list
[compute]
region = europe-west1
zone = europe-west1
[core]
account = virginia@viralmar.es
disable_usage_reporting = False
project = kubernetes-tests-194217

Your active configuration is: [default]
```

List confugurations

```
$ gcloud config configurations list
NAME     IS_ACTIVE  ACCOUNT               PROJECT                  DEFAULT_ZONE  DEFAULT_REGION
default  True       virginia@viralmar.es  kubernetes-tests-194217  europe-west1  europe-west1
```

Create a new config

```
$ gcloud config configurations create autosuggest
Created [autosuggest].
Activated [autosuggest].
```

Init this new config

```
$ gcloud config init
Welcome! This command will take you through the configuration of gcloud.

Settings from your current configuration [autosuggest] are:
core:
  disable_usage_reporting: 'False'

Pick configuration to use:
 [1] Re-initialize this configuration [autosuggest] with new settings 
 [2] Create a new configuration
 [3] Switch to and re-initialize existing configuration: [default]
```

# Set compute region and zone

```
$  gcloud config set compute/region europe-west1
$  gcloud config set compute/zone europe-west1
```

# Create project

```
$ gcloud projects create autosuggest
```

# Set configuration project

```
$ gcloud config set core/project autosuggest
```

```
$ gcloud projects list
PROJECT_ID               NAME              PROJECT_NUMBER
autosuggest-194816       autosuggest       833674540613
kubernetes-tests-194217  kubernetes tests  31009234571
valiant-marker-193213    My First Project  341154997936
```

# Create a Maven project for autosuggest

```
export GCP_PROJECT_ID=autosuggest-194816
export BUCKET_NAME=autosuggest-bucket
export BEAM_PROJECT=autosuggest-beam
export BEAM_PROJECT_GRP_ID=org.example
export BEAM_PROJECT_PKG=org.example.autosuggest
```

APACHE
mvn archetype:generate \
      -DarchetypeGroupId=org.apache.beam \
      -DarchetypeArtifactId=beam-sdks-java-maven-archetypes-examples \
      -DarchetypeVersion=2.2.0 \
      -DgroupId=${BEAM_PROJECT_GRP_ID} \
      -DartifactId=${BEAM_PROJECT} \
      -Dversion="0.1" \
      -Dpackage=${BEAM_PROJECT_PKG} \
      -DinteractiveMode=false
```

GPC
```
mvn archetype:generate \
      -DarchetypeGroupId=com.google.cloud.dataflow \
      -DarchetypeArtifactId=google-cloud-dataflow-java-archetypes-examples \
      -DarchetypeVersion=2.2.0 \
      -DgroupId=${BEAM_PROJECT_GRP_ID} \
      -DartifactId=${BEAM_PROJECT} \
      -Dversion="0.1" \
      -Dpackage=${BEAM_PROJECT_PKG} \
      -DinteractiveMode=false

      
```
# Execute pipeline locally

```
mvn compile exec:java -Dexec.mainClass=org.example.suggest.WordCount \
     -Dexec.args="--inputFile=pom.xml --output=counts" -Pdirect-runner
```

# Enable APIs for DataFlow

 Cloud Dataflow, Compute Engine, Stackdriver Logging, Google Cloud Storage, Google Cloud Storage JSON, BigQuery, Google Cloud Pub/Sub, Google Cloud Datastore, and Google Cloud Resource Manager

```
gcloud services enable dataflow.googleapis.com compute.googleapis.com stackdriver.googleapis.com storage-component.googleapis.com storage-api.googleapis.com bigquery-json.googleapis.com pubsub.googleapis.com datastore.googleapis.com cloudresourcemanager.googleapis.com
```

# Create a bucket

Multi-Regional bucket => Multiregional locations

Multi-Region Name 	Multi-Region Description
=================   ========================
asia 	              Asia Pacific
eu 	                European Union
us 	                United States

```
$ gcloud projects list
PROJECT_ID               NAME              PROJECT_NUMBER
autosuggest-194816       autosuggest       833674540613
kubernetes-tests-194217  kubernetes tests  31009234571
valiant-marker-193213    My First Project  341154997936

$ gsutil mb -p autosuggest-194816 -c multi_regional -l eu gs://${BUCKET_NAME}/
```

# Execute on GCP

```
mvn compile exec:java \
      -Dexec.mainClass=org.example.autosuggest.WordCount \
      -Dexec.args="--project=${GCP_PROJECT_ID} \
      --stagingLocation=gs://${BUCKET_NAME}/staging/ \
      --gcpTempLocation=gs://${BUCKET_NAME}/temp/ \
      --output=gs://${BUCKET_NAME}/output \
      --runner=DataflowRunner \
      --jobName=${BEAM_PROJECT}"



mvn compile exec:java \
  -Dexec.mainClass=com.example.WordCount \
  -Dexec.args="--project=autosuggest-194816 \
  --stagingLocation=gs://autosuggest-194816/staging/ \
  --output=gs://autosuggest-194816/output \
  --runner=DataflowRunner \
  --jobName=dataflow-intro"


```

# List of APIs
places-backend.googleapis.com                  Google Places API Web Service
clouderrorreporting.googleapis.com             Stackdriver Error Reporting API
analyticsreporting.googleapis.com              Google Analytics Reporting API
youtube.googleapis.com                         YouTube Data API v3
adsense.googleapis.com                         AdSense Management API
sqladmin.googleapis.com                        Google Cloud SQL API
genomics.googleapis.com                        Genomics API
adexchangebuyer.googleapis.com                 Ad Exchange Buyer API II
dlp.googleapis.com                             DLP API
plushangouts.googleapis.com                    Google+ Hangouts API
toolresults.googleapis.com                     Cloud Tool Results API
monitoring.googleapis.com                      Stackdriver Monitoring API
cloudkms.googleapis.com                        Google Cloud Key Management Service (KMS) API
gamesmanagement.googleapis.com                 Google Play Game Management
pagespeedonline.googleapis.com                 PageSpeed Insights API
gamesconfiguration.googleapis.com              Google Play Game Services Publishing API
freebase.googleapis.com                        Freebase API
surveys.googleapis.com                         Surveys API
sql-component.googleapis.com                   Google Cloud SQL
appengine.googleapis.com                       Google App Engine Admin API
embeddedassistant.googleapis.com               Google Assistant API
mobilecrashreporting.googleapis.com            Mobile Crash Reporting API
spectrum.googleapis.com                        Google Spectrum Database API
webmasters.googleapis.com                      Google Search Console API
androidcheck.googleapis.com                    Android Device Verification
cloudmonitoring.googleapis.com                 Google Cloud Monitoring API
licensing.googleapis.com                       Enterprise License Manager API
sheets.googleapis.com                          Google Sheets API
groupssettings.googleapis.com                  Groups Settings API
gan.googleapis.com                             Google Affiliate Network API
distance-matrix-backend.googleapis.com         Google Maps Distance Matrix API
tpu.googleapis.com                             Cloud TPU API
storage-component.googleapis.com               Google Cloud Storage
iam.googleapis.com                             Google Identity and Access Management (IAM) API
spanner.googleapis.com                         Cloud Spanner API
staging-moviesanywhere.sandbox.googleapis.com  Staging Movies Anywhere API
ml.googleapis.com                              Google Cloud Machine Learning Engine
carddav.googleapis.com                         Google Contacts CardDAV API
deploymentmanager.googleapis.com               Google Cloud Deployment Manager V2 API
audit.googleapis.com                           Audit API
cloudresourcemanager.googleapis.com            Google Cloud Resource Manager API
doubleclicksearch.googleapis.com               DoubleClick Search API
firebasedynamiclinks.googleapis.com            Firebase Dynamic Links API
abusiveexperiencereport.googleapis.com         Google Abusive Experience Report API
proximitybeacon.googleapis.com                 Google Proximity Beacon API
cloudlatencytest.googleapis.com                Cloud Network Performance Monitoring API
appengineflex.googleapis.com                   Google App Engine Flexible Environment
language.googleapis.com                        Google Cloud Natural Language API
plusdomains.googleapis.com                     Google+ Domains API
zync.googleapis.com                            Zync Render API
actions.googleapis.com                         Google Actions API
moviesanywhere.googleapis.com                  Play Movies Anywhere API
identitytoolkit.googleapis.com                 Identity Toolkit API
stackdriver.googleapis.com                     Stackdriver API
googlecloudmessaging.googleapis.com            Google Cloud Messaging
partners.googleapis.com                        Google Partners API
youtubeadsreach.googleapis.com                 YouTube Ads Reach API
firebase.googleapis.com                        Firebase Services API
serviceconsumermanagement.googleapis.com       Service Consumer Management API
street-view-image-backend.googleapis.com       Google Street View Image API
endpoints.googleapis.com                       Google Cloud Endpoints
dns.googleapis.com                             Google Cloud DNS API
dataflow.googleapis.com                        Google Dataflow API
customsearch.googleapis.com                    Custom Search API
bigtabletableadmin.googleapis.com              Google Cloud Bigtable Table Admin API
chromewebstore.googleapis.com                  Chrome Web Store API
androidenterprise.googleapis.com               Google Play EMM API
cloudiot.googleapis.com                        Google Cloud IoT API
vault.googleapis.com                           Google Vault API
urlshortener.googleapis.com                    URL Shortener API
calendar-json.googleapis.com                   Google Calendar API
admin.googleapis.com                           Admin SDK
adexchangebuyer-json.googleapis.com            Ad Exchange Buyer API
picker.googleapis.com                          Google Picker API
storage-api.googleapis.com                     Google Cloud Storage JSON API
webfonts.googleapis.com                        Web Fonts Developer API
bigquerydatatransfer.googleapis.com            BigQuery Data Transfer API
logging.googleapis.com                         Stackdriver Logging API
geolocation.googleapis.com                     Google Maps Geolocation API
resourceviews.googleapis.com                   Google Compute Engine Instance Groups API
doubleclickbidmanager.googleapis.com           DoubleClick Bid Manager API
replicapool.googleapis.com                     Google Compute Engine Instance Group Manager API
clouddebugger.googleapis.com                   Stackdriver Debugger API
cloudbuild.googleapis.com                      Google Cloud Container Builder API
servicecontrol.googleapis.com                  Google Service Control API
usercontext.googleapis.com                     Awareness API
container.googleapis.com                       Google Kubernetes Engine API
oslogin.googleapis.com                         Google Cloud OS Login API
digitalassetlinks.googleapis.com               Digital Asset Links API
containerregistry.googleapis.com               Google Container Registry API
safebrowsing-json.googleapis.com               Safe Browsing API (Legacy)
cloudapis.googleapis.com                       Google Cloud APIs
translate.googleapis.com                       Google Cloud Translation API
partners-json.googleapis.com                   Google Partners API
androiddeviceprovisioning.googleapis.com       Android Device Provisioning Partner API
gmail.googleapis.com                           Gmail API
pubsub.googleapis.com                          Google Cloud Pub/Sub API
drive.googleapis.com                           Google Drive API
travelpartner.googleapis.com                   Travel Hotel Ads API
firebaserules.googleapis.com                   Firebase Rules API
appsactivity.googleapis.com                    Apps Activity API
speech.googleapis.com                          Google Cloud Speech API
cloudtrace.googleapis.com                      Stackdriver Trace API
youtubeanalytics.googleapis.com                YouTube Analytics API
tagmanager.googleapis.com                      Tag Manager API
copresence.googleapis.com                      Nearby Messages API
static-maps-backend.googleapis.com             Google Static Maps API
tasks.googleapis.com                           Tasks API
cloudfunctions.googleapis.com                  Google Cloud Functions API
bigtableadmin.googleapis.com                   Cloud Bigtable Admin API
siteverification.googleapis.com                Site Verification API
runtimeconfig.googleapis.com                   Google Cloud Runtime Configuration API
roads.googleapis.com                           Google Maps Roads API
playcustomapp.googleapis.com                   Google Play Custom App Publishing API
bigtable.googleapis.com                        Cloud Bigtable API
reseller.googleapis.com                        Google Apps Reseller API
slides.googleapis.com                          Google Slides API
contacts.googleapis.com                        Contacts API
maps-ios-backend.googleapis.com                Google Maps SDK for iOS
directions-backend.googleapis.com              Google Maps Directions API
dialogflow.googleapis.com                      Dialogflow API
realtime.googleapis.com                        Google Realtime API
maps-backend.googleapis.com                    Google Maps JavaScript API
replicapoolupdater.googleapis.com              Google Compute Engine Instance Group Updater API
classroom.googleapis.com                       Google Classroom API
manufacturers.googleapis.com                   Manufacturer Center API
sourcerepo.googleapis.com                      Cloud Source Repositories API
placesios.googleapis.com                       Google Places API for iOS
cloudresourcesearch.googleapis.com             Google Cloud Resource Search
prediction.googleapis.com                      Prediction API
safebrowsing.googleapis.com                    Google Safe Browsing API
videointelligence.googleapis.com               Cloud Video Intelligence API
homegraph.googleapis.com                       Google HomeGraph API
elevation-backend.googleapis.com               Google Maps Elevation API
fitness.googleapis.com                         Fitness API
adexchangeseller.googleapis.com                Ad Exchange Seller API
maps-android-backend.googleapis.com            Google Maps Android API
adsensehost.googleapis.com                     AdSense Host API
maps-embed-backend.googleapis.com              Google Maps Embed API
mirror.googleapis.com                          Google Mirror API
servicemanagement.googleapis.com               Google Service Management API
people.googleapis.com                          Google People API
storagetransfer.googleapis.com                 Google Storage Transfer API
adexperiencereport.googleapis.com              Google Ad Experience Report API
appsmarket.googleapis.com                      Google Apps Marketplace API
dfareporting.googleapis.com                    DCM/DFA Reporting And Trafficking API
blogger.googleapis.com                         Blogger API v3
kgsearch.googleapis.com                        Knowledge Graph Search API
vision.googleapis.com                          Google Cloud Vision API
appsmarket-component.googleapis.com            Google Apps Marketplace SDK
plus.googleapis.com                            Google+ API
books.googleapis.com                           Google Books API
script.googleapis.com                          Google Apps Script API
timezone-backend.googleapis.com                Google Maps Time Zone API
fcm.googleapis.com                             Firebase Cloud Messaging API
datastore.googleapis.com                       Google Cloud Datastore API
analytics.googleapis.com                       Analytics API
geocoding-backend.googleapis.com               Google Maps Geocoding API
acceleratedmobilepageurl.googleapis.com        Accelerated Mobile Pages (AMP) URL API
groupsmigration.googleapis.com                 Groups Migration API
androidpublisher.googleapis.com                Google Play Android Developer API
placesandroid.googleapis.com                   Google Places API for Android
caldav.googleapis.com                          CalDAV API
cloudshell.googleapis.com                      Cloud Shell API
testing.googleapis.com                         Google Cloud Testing API
securetoken.googleapis.com                     Token Service API
androidmanagement.googleapis.com               Android Management API
civicinfo.googleapis.com                       Google Civic Information API
searchconsole.googleapis.com                   Google Search Console URL Testing Tools API
androidovertheair.googleapis.com               Google Android Over the Air API
youtubereporting.googleapis.com                YouTube Reporting API
compute.googleapis.com                         Google Compute Engine API
dataproc.googleapis.com                        Google Cloud Dataproc API
cloudbilling.googleapis.com                    Google Cloud Billing API
streetviewpublish.googleapis.com               Street View Publish API
bigquery-json.googleapis.com                   BigQuery API
fusiontables.googleapis.com                    Fusion Tables API
shoppingcontent.googleapis.com                 Content API for Shopping
firebaseremoteconfig.googleapis.com            Firebase Remote Config API
firestore.googleapis.com                       Google Cloud Firestore API
orkut.googleapis.com                           Orkut REST API
games.googleapis.com                           Google Play Game Services
poly.googleapis.com                            Poly API
