# Sonatype IQ server

TBD

## Installing License

The license file can be installed via the UI when IQ server is running, or it can be done as a part of the deploy. To do it automatically, first encode your `.lic` file in Base 64 with no line breaks, eg:

```bash
base64 --wrap=0 mylicense.lic > lic.base64
```

Then add this value to your `Values.yaml` file as `iq.licenseSecret`, eg:

```YAML
iq:
  licenseSecret: bXkgc2FtcGxlIGxpY2Vuc2U=
```

Ensure that in your values in `iq.configYaml` has

```yaml
licenseFile: /etc/nexus-iq-license/license_lic
```

If you leave this entry blank, IQ Server will start but it will prompt you for a license when you first enter the GUI.
