## 📖 Table of Contents
1. [🌍 Localstack Terraform Test Runner 🚀](#-localstack-terraform-test-runner-)
2. [🎯 Purpose](#-purpose)
3. [🔧 Installation](#-installation)
4. [🏃‍ How to Run](#-how-to-run)
5. [🔍 How to Run Test Cases](#-how-to-run-test-cases)
6. [🔢 Default Environment Variables for Terraform Tests](#-default-environment-variables-for-terraform-tests)
7. [⚙️ Options](#-options)
8. [🔐 Services](#-services)

---

## 🌍 **Localstack Terraform Test Runner** 🚀

This utility serves as a test runner designed specifically for Localstack and Terraform. By leveraging it, users can execute test cases from the Hashicorp Terraform provider AWS against a Localstack Instance.

## 🎯 **Purpose**

The primary objective behind this project is to segregate test cases from the Localstack repo and execute them against Localstack. This helps in obtaining parity metrics.

---

## 🔧 **Installation**

1. 📦 Clone the repository (including submodules):
```
git clone git@github.com:localstack/localstack-terraform-test.git --recurse-submodules
```

2. 🔀 Ensure you're on the latest version of the submodules:
```
git submodule update --init --recursive
```

3. 🚀 Install dependencies:
```
make install
```

---

## 🏃‍♂️ **How to Run**

- 🔑 (Pro-image only) Set the `LOCALSTACK_AUTH_TOKEN` environment variable.
- Construct a testing binary for the Golang module:
```
python -m terraform_pytest.main build -s s3
```
- Now you're all set to utilize the `python -m pytest` commands to list and execute test cases derived from Golang.

---

## 🔍 **How to Run Test Cases**

- 📋 List all test cases from a specific service:
```
python -m pytest terraform-provider-aws/internal/service/<service> --collect-only -q
```
- 🚀 Execute a particular test case:
```
python -m pytest terraform-provider-aws/internal/service/<service>/<test-file> -k <test-case-name> --ls-start
```
_or_
```
python -m pytest terraform-provider-aws/internal/service/<service>/<test-file>::<test-case-name> --ls-start
```
- You can prepend additional environment variables to the command. For instance:
```
AWS_ALTERNATE_REGION='us-west-2' python -m pytest terraform-provider-aws/internal/service/<service>/<test-file>::<test-case-name> --ls-start
```

---

## 🔢 **Default Environment Variables for Terraform Tests**

| Variable                          | Default Value         |
|-----------------------------------|-----------------------|
| `TF_ACC`                          | 1                     |
| `AWS_ACCESS_KEY_ID`               | test                  |
| `AWS_SECRET_ACCESS_KEY`           | test                  |
| `AWS_DEFAULT_REGION`              | us-west-1             |
| `AWS_ALTERNATE_ACCESS_KEY_ID`     | test                  |
| `AWS_ALTERNATE_SECRET_ACCESS_KEY` | test                  |
| `AWS_ALTERNATE_REGION`            | us-east-2             |
| `AWS_THIRD_SECRET_ACCESS_KEY`     | test                  |
| `AWS_THIRD_ACCESS_KEY_ID`         | test                  |
| `AWS_THIRD_REGION`                | eu-west-1             |
| `AWS_ENDPOINT_URL`                | http://localhost:4566 |

---

## ⚙️ **Options**

- `--ls-start`: Initializes the Localstack instance before test case execution. It triggers the CLI:
```
localstack start -d
```

- `--gather-metrics`: Gathers raw test metrics for a specific run. But first, make sure you manually install the extension:
```
localstack extensions init
localstack extensions install "git+https://github.com/localstack/localstack-moto-test-coverage/#egg=collect-raw-metric-data-extension&subdirectory=collect-raw-metric-data-extension"
```
Remember to set the `SERVICE` environment variable for naming the metric file.

---

## 🔐 **Services**

Executing this test suite is a time-intensive process. To cater to this, the following mechanisms are in place:


| Mechanism     | Description                                                                                                                                                   |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Blacklisting**  | Services devoid of tests are blacklisted to avoid needless execution.                                                                                         |
| **Ignored**       | Services might have test cases, but if they all fail leading to timeouts, they're marked as non-functional and bypassed. Refer to `terraform_pytest/utils.py`. |
| **Partitioning**  | Some services are extensive and get divided into partitions. Each partition holds a unique subset of tests for that particular service.                          |
