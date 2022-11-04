# gcc-glibc

<!---
Note: Do NOT edit directly, this file was generated using https://github.com/chainguard-images/readme-generator
-->

[![CI status](https://github.com/chainguard-images/gcc-glibc/actions/workflows/release.yaml/badge.svg)](https://github.com/chainguard-images/gcc-glibc/actions/workflows/release.yaml)

Minimal container image for building C applications (which require glibc).

## Get It!

The image is available on `cgr.dev`:

```
docker pull cgr.dev/chainguard/gcc-glibc:latest
```

## Supported tags

| Tag | Digest | Arch |
| --- | ------ | ---- |
| `12` `12-bullseye` `12.2` `12.2-bullseye` `12.2.0` `12.2.0-bullseye` `12.2.0-r6` `bullseye` `latest` | `sha256:f2f5b4efee09da8ad4c0540a30953f4d815efff034d4607261c68e00d5903d28`<br/>[View entry in Rekor](https://rekor.tlog.dev/?hash=sha256:f2f5b4efee09da8ad4c0540a30953f4d815efff034d4607261c68e00d5903d28) | `amd64` |


## Usage

To build the C application in [examples/hello/main.c](examples/hello/main.c):

```
$ docker run --rm -v "${PWD}:/work" cgr.dev/chainguard/gcc-glibc examples/hello/main.c -o hello
```

This will write a Linux binary to `./hello`. If you're on Linux and have the glibc library, you
should be able to run it directly. Otherwise you can run it in a container e.g:

```
$ docker run --rm -v "$PWD/hello:/hello" --platform linux/amd64 cgr.dev/chainguard/glibc-dynamic /hello
Hello World!
```

Note: only `linux/amd64` is uspported at the moment.

We can also do this all in a multi-stage Dockerfile e.g:

```Dockerfile
FROM cgr.dev/chainguard/gcc-glibc as build

COPY hello.c /work/hello.c
RUN cc hello.c -o hello

FROM cgr.dev/chainguard/glibc-dynamic

COPY --from=build /work/hello /hello
CMD ["/hello"]
```

And we can also compile statically to be used in environments without glibc:


```Dockerfile
FROM cgr.dev/chainguard/gcc-glibc as build

COPY hello.c /work/hello.c
RUN cc --static hello.c -o hello

FROM cgr.dev/chainguard/static

COPY --from=build /work/hello /hello
CMD ["/hello"]
```


## Signing

All Chainguard Images are signed using [Sigstore](https://sigstore.dev)!

<details>
<br/>
To verify the image, download <a href="https://github.com/sigstore/cosign">cosign</a> and run:

```
COSIGN_EXPERIMENTAL=1 cosign verify cgr.dev/chainguard/gcc-glibc:latest | jq
```

Output:
```
Verification for cgr.dev/chainguard/gcc-glibc:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - Any certificates were verified against the Fulcio roots.
[
  {
    "critical": {
      "identity": {
        "docker-reference": "ghcr.io/chainguard-images/gcc-glibc"
      },
      "image": {
        "docker-manifest-digest": "sha256:f2f5b4efee09da8ad4c0540a30953f4d815efff034d4607261c68e00d5903d28"
      },
      "type": "cosign container image signature"
    },
    "optional": {
      "1.3.6.1.4.1.57264.1.1": "https://token.actions.githubusercontent.com",
      "1.3.6.1.4.1.57264.1.2": "schedule",
      "1.3.6.1.4.1.57264.1.3": "456addbf607141f4e65b0914aa643e8b9f57b0af",
      "1.3.6.1.4.1.57264.1.4": "Create Release",
      "1.3.6.1.4.1.57264.1.5": "chainguard-images/gcc-glibc",
      "1.3.6.1.4.1.57264.1.6": "refs/heads/main",
      "Bundle": {
        "SignedEntryTimestamp": "MEQCIFUvKtsc4O/wtbc/QOUzOCzpumT8Tf2Fik+C14byyKCXAiALKB0kKh6WSGMBfmggDkdC07y/jN0VpR9ZCtgfjgqocg==",
        "Payload": {
          "body": "eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiJjYmRhNDkzOTQ5NWQ5NjBiNmM2M2Q0ZTQ3YTNmZjFlNjE5YWVhODUxMDE3ZWYwNmIxZDFmZTJkOTVjODQyYTlmIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FUUNJQlVCcHMyN1J5akd4VUhpM3ZXRWFSdUdleDFNVnIrU25VVm1VWUg4RGJ1MEFpQndBVGVrR0FvRTdXd2pMU096eGwxd1JKVHEzRHFkK1Zld3RhcGZPckIzUFE9PSIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCRFJWSlVTVVpKUTBGVVJTMHRMUzB0Q2sxSlNVUnpWRU5EUVhwbFowRjNTVUpCWjBsVlJVZzFSREJKT1dZdmJtWldlWFJXY1dvMlZIYzRRMDFwYkZORmQwTm5XVWxMYjFwSmVtb3dSVUYzVFhjS1RucEZWazFDVFVkQk1WVkZRMmhOVFdNeWJHNWpNMUoyWTIxVmRWcEhWakpOVWpSM1NFRlpSRlpSVVVSRmVGWjZZVmRrZW1SSE9YbGFVekZ3WW01U2JBcGpiVEZzV2tkc2FHUkhWWGRJYUdOT1RXcEplRTFVUVRCTlJFbDRUVlJKTlZkb1kwNU5ha2w0VFZSQk1FMUVTWGxOVkVrMVYycEJRVTFHYTNkRmQxbElDa3R2V2tsNmFqQkRRVkZaU1V0dldrbDZhakJFUVZGalJGRm5RVVUyV0VaNmVFMU9VbG8xZEVaNlpVWm9TVWR2ZFcxcFZYbGxRMk4wY3pWQmVIUnVOV2NLZEV0bU5rSTRWVkZhUnpWVVJFaEhaWGhyTjI1MGRUUnRURU5DVkhkdlRFbFBTMGhZY0dJNVNVTjZSa2hpUVRWUFluRlBRMEZzV1hkblowcFRUVUUwUndwQk1WVmtSSGRGUWk5M1VVVkJkMGxJWjBSQlZFSm5UbFpJVTFWRlJFUkJTMEpuWjNKQ1owVkdRbEZqUkVGNlFXUkNaMDVXU0ZFMFJVWm5VVlUwY2taM0NtUlVVVTkwVTNaSGFWTkRWV2REZG1FMGQxaG1VVkozZDBoM1dVUldVakJxUWtKbmQwWnZRVlV6T1ZCd2VqRlphMFZhWWpWeFRtcHdTMFpYYVhocE5Ga0tXa1E0ZDJGM1dVUldVakJTUVZGSUwwSkhSWGRZTkZwa1lVaFNNR05JVFRaTWVUbHVZVmhTYjJSWFNYVlpNamwwVERKT2IxbFhiSFZhTTFab1kyMVJkQXBoVnpGb1dqSldla3d5WkdwWmVURnVZa2RzYVZsNU9IVmFNbXd3WVVoV2FVd3paSFpqYlhSdFlrYzVNMk41T1hsYVYzaHNXVmhPYkV4dWJHaGlWM2hCQ21OdFZtMWplVGx2V2xkR2EyTjVPWFJaVjJ4MVRVUnJSME5wYzBkQlVWRkNaemM0ZDBGUlJVVkxNbWd3WkVoQ2VrOXBPSFprUnpseVdsYzBkVmxYVGpBS1lWYzVkV041Tlc1aFdGSnZaRmRLTVdNeVZubFpNamwxWkVkV2RXUkROV3BpTWpCM1JtZFpTMHQzV1VKQ1FVZEVkbnBCUWtGblVVbGpNazV2V2xkU01RcGlSMVYzVG1kWlMwdDNXVUpDUVVkRWRucEJRa0YzVVc5T1JGVXlXVmRTYTFsdFdUSk5SR040VGtSR2JVNUhWVEpPVjBsM1QxUkZNRmxYUlRKT1JFNXNDazlIU1RWYWFsVXpXV3BDYUZwcVFXTkNaMjl5UW1kRlJVRlpUeTlOUVVWRlFrRTFSR050Vm1oa1IxVm5WVzFXYzFwWFJucGFWRUZ3UW1kdmNrSm5SVVVLUVZsUEwwMUJSVVpDUW5ScVlVZEdjR0p0WkRGWldFcHJURmRzZEZsWFpHeGplVGx1V1RKTmRGb3llSEJaYlUxM1NGRlpTMHQzV1VKQ1FVZEVkbnBCUWdwQ1oxRlFZMjFXYldONU9XOWFWMFpyWTNrNWRGbFhiSFZOU1VkTFFtZHZja0puUlVWQlpGbzFRV2RSUTBKSWQwVmxaMEkwUVVoWlFUTlVNSGRoYzJKSUNrVlVTbXBIVWpSamJWZGpNMEZ4U2t0WWNtcGxVRXN6TDJnMGNIbG5Remh3TjI4MFFVRkJSMFZSUjJoVU4wRkJRVUpCVFVGU2VrSkdRV2xDTlZod2JqZ0tiVWR6YmtsblduRkVXa3hvVlZwRVRtMHZSMVJzWm5kdFNUZDNaMUV4V1daTFYxUnhWbmRKYUVGTmJHb3lTV0ZGWmk5dGFFbFlOalpWTDNSSVdUVXZRUXBVYzAxT01WUnBTbmhITUVoVWREaENTWEZGZWsxQmIwZERRM0ZIVTAwME9VSkJUVVJCTW1kQlRVZFZRMDFGTjJrMVFucHRla3BpZVhFMlMxQkJUbHB6Q2pKdVFWSjNjMEZTVlhsV2RXd3pNRlZETm1vellrVTVUbTR2YmxkR05WbGljVlJyVFVoelJGQk5aemhsY1VGSmVFRk5UazF0Ym5jNVluaENNRmRXUjFjS09VaG1kRGRUWkVWcE5FaEdSREZTZFV0emIwdEJPV0p5WkhWRlEwUjRNVWh6VjFrd2N6a3pTSFJ3ZVRrMWNVRTBNSGM5UFFvdExTMHRMVVZPUkNCRFJWSlVTVVpKUTBGVVJTMHRMUzB0Q2c9PSJ9fX19",
          "integratedTime": 1667527893,
          "logIndex": 6461511,
          "logID": "c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"
        }
      },
      "Issuer": "https://token.actions.githubusercontent.com",
      "Subject": "https://github.com/chainguard-images/gcc-glibc/.github/workflows/release.yaml@refs/heads/main",
      "githubWorkflowName": "Create Release",
      "githubWorkflowRef": "refs/heads/main",
      "githubWorkflowRepository": "chainguard-images/gcc-glibc",
      "githubWorkflowSha": "456addbf607141f4e65b0914aa643e8b9f57b0af",
      "githubWorkflowTrigger": "schedule",
      "run_attempt": "1",
      "run_id": "3390719393",
      "sha": "456addbf607141f4e65b0914aa643e8b9f57b0af"
    }
  }
]
```

You can verify that the image was built in Github Actions in this repository from the `Issuer` and `Subject` fields.
</details>

## Build

This image is built with [apko](https://github.com/chainguard-dev/apko).

