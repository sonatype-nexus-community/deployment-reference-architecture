# Sonatype IQ server

[Sonatype Nexus IQ Server](https://www.sonatype.com/nexus-iq-server)  is everything you need to know to trust your software supply chain. It powers Nexus Firewall, Nexus Lifecycle, and Nexus Auditor.


### With Open Docker Image

By default, the Chart uses Red Hat's Certified Container. If you want to use the standard docker image, run with `--set iq.imageName=sonatype/nexus-iq-server`.

### With Red Hat Certified container

Red Hat Certified Container (RHCC) requires authentication in order to pull the image. To do this:

  1. [Create a Service Account](https://access.redhat.com/terms-based-registry/)
  2. Copy the docker configuration JSON sample and replace the host from `registry.redhat.io` to `registry.connect.redhat.com` and save it as a file, eg:

```json
{
  "auths": {
    "registry.connect.redhat.com": {
      "auth": "TOKEN"
    }
  }
}
```
  3. Encode the file in Base 64 format:

```bash
cat service-auth.json | base64 > service.base64
```
  4. Add this to your `myvalues.yaml` as `iq.imagePullSecret`:

```yaml
iq:
  name: nxiq
  imageName: registry.connect.redhat.com/sonatype/nexus-iq-server
  imageTag: 1.85.0-01-ubi
  imagePullPolicy: IfNotPresent
  imagePullSecret: "{BASE64-DOCKER-CONFIG}"
```


## Chart Configuration Options

| Parameter            | Description                                                  | Default           |
| -------------------- | ------------------------------------------------------------ | ----------------- |
| `iq.imageName`       | The image name to use for the IQ Container, eg `sonatype/nexus-iq-server`  | `"registry.connect.redhat.com/sonatype/nexus-iq-server"`              |
| `iq.imageTag`        | The image tag to use                                         | the latest tag, eg `"1.85.0-01-ubi"`              |
| `iq.imagePullSecret` | The base-64 encoded secret to pull a container from Red Hat  | `""`              |
| `iq.applicationPort` | Port of the application connector. Must match the value in the `configYaml` property | `8070`            |
| `iq.adminPort`       | Port of the application connector. Must match the value in the `configYaml` property | `8071`            |
| `iq.storageCapacity` | The amount of drive space to allocate                        | `1Gi`             |
| `iq.memory`          | The amount of RAM to allocate                                | `1Gi`             |
| `iq.licenseSecret`   | The base-64 encoded license file to be installed at startup  | `""`              |
| `iq.configYaml`      | A YAML block which will be used as a configuration block for IQ Server. | See `values.yaml` |
| `ingress.enabled`                           | Create an ingress for Nexus         | `true`                                  |
| `ingress.annotations`                       | Annotations to enhance ingress configuration  | `{}`                          |
| `ingress.tls.enabled`                       | Enable TLS                          | `true`                                 |
| `ingress.tls.secretName`                    | Name of the secret storing TLS cert, `false` to use the Ingress' default certificate | `nexus-tls`                             |
| `ingress.path`                              | Path for ingress rules. GCP users should set to `/*` | `/`                    |
| `deployment.preStart.command`               | Command to run before starting the IQ Server container  | `nil`                   |
| `deployment.postStart.command`              | Command to run after starting the IQ Server container  | `nil`                    |
| `deployment.terminationGracePeriodSeconds`  | Update termination grace period (in seconds)        | 120s                    |



## Configuring IQ Server

You can define the `config.yml` for IQ Server in your `myvalues.yml` file on startup. It is the `iq.configYaml` property. For more details, see the [Configuring IQ Server](https://help.sonatype.com/iqserver/configuring) help page.


## Installing License

The license file can be installed via the UI when IQ server is running, or it can be done as a part of the deploy. To do it automatically, first encode your `.lic` file in Base 64 with no line breaks, eg:

```bash
base64 --wrap=0 mylicense.lic > lic.base64
```

Then add this value to your `myvalues.yaml` file as `iq.licenseSecret`, eg:

```yaml
iq:
  licenseSecret: bXkgc2FtcGxlIGxpY2Vuc2U=
```

Ensure that in your values in `iq.configYaml` has

```yaml
licenseFile: /etc/nexus-iq-license/license_lic
```

If you leave this entry blank, IQ Server will start but it will prompt you for a license when you first enter the GUI.
