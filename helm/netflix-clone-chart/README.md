
## Netflix Clone Helm Chart

This Helm chart deploys a Netflix clone application.

## Prerequisites

- Kubernetes 1.12+
- Helm 3.0+

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
helm install my-release netflix-clone-chart/
```

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
helm uninstall my-release
```

## Notes

After installation, you can access the application using the service or ingress defined in the chart. Refer to the `templates/NOTES.txt` file for more details on accessing the application.

## License

This project is licensed under the MIT License. See the LICENSE file for details.