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

# Create a Maven project for autosuggest

```

export BEAM_PROJECT=autosuggest-beam
export BEAM_PROJECT_GRP_ID=org.example
export BEAM_PROJECT_PKG=org.example.autosuggest
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


