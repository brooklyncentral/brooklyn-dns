# Perform releases

## SNAPSHOT releases

`master` branch should only contains `SNAPSHOT` version. Upon every commit pushed on `master`, Circle CI will build, sign
then deploy `SNAPSHOT` version to sonatype automatically.

## Official releases

To perform an official release, simply create a branch `release/<version>`, update the version number in the code then push
your changes. Circle CI will build, sign then deploy artifacts to sonatype automatically.

You will then need to go to the [sonatype UI](https://oss.sonatype.org/#stagingRepositories) to manually
[close and release artifacts](http://central.sonatype.org/pages/releasing-the-deployment.html).

## Manual releases

You can always perform manual releases by running the following command:

```bash
mvn clean deploy -DskipTests -DdeployTo=sonatype --settings .circleci/settings.xml
```

*If your project contains java classes, **don't forget to add** `source:jar javadoc:jar` to the deploy command above!*

Note that you need to setup the following environment variables:


| Environment variable | Value                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------- |
| SONATYPE_USER        | Sonatype user                                                                            |
| SONATYPE_PASSWORD    | Sonatype password                                                                        |
| GPG_EXECUTABLE       | GPG executable, e.g. `gpg`                                                               |
| GPG_SECRET_KEY       | GPG secret key to use. You can get it be running: `gpg -a --export-secret-keys <KEY-ID>` |
| GPG_PASSPHRASE       | The passphrase of the <KEY-ID>                                                           |
