# Microsoft Presidio Java SDK

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Java](https://img.shields.io/badge/java-17-blue.svg)

A Java SDK for Microsoft Presidio, enabling seamless integration with Presidio's powerful data anonymization and analysis capabilities via HTTP Client.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Contributing](#contributing)
- [License](#license)

## Introduction

The Microsoft Presidio Java SDK provides a robust and easy-to-use interface for interacting with Microsoft Presidio's API. Whether you're looking to analyze sensitive information
or anonymize data within your Java applications, this SDK simplifies the process, allowing you to focus on building impactful solutions.

## Features

- **Analyze Data:** Identify and analyze sensitive information within text.
- **Anonymize Data:** Apply various anonymization strategies such as encryption.
- **Deanonymize Data:** Reverse anonymization processes when needed.
- **Easy Integration:** Simple setup and usage with minimal configuration.
- **Extensible:** Supports custom anonymization strategies.

## Installation

Add the SDK to your project by including the following dependency in your `pom.xml` (for Maven):

```xml

<dependency>
  <groupId>com.hexploits.oss.sdk</groupId>
  <artifactId>presidio</artifactId>
  <version>UPDATE_TO_LATEST_TAG</version>
</dependency>
```

Or, if you're using Gradle:

```groovy
implementation 'com.hexploits.oss.sdk:presidio:UPDATE_TO_LATEST_TAG'
```

## Usage

Below is an example of how to use the SDK to analyze, anonymize, and deanonymize text data.

```java
public static void main(String[] args) throws IOException {
    PresidioAnalyzerClient analyzerClient = new PresidioAnalyzerClient(
      new PresidioAnalyzerClientDetails("http://localhost:5001")
    );
    PresidioAnonymizerClient anonymizerClient = new PresidioAnonymizerClient(
      new PresidioAnonymizerClientDetails("http://localhost:5002")
    );

    // Analyze
    AnalyzeRequest req = AnalyzeRequest.builder()
      .text("My name is Cameron, I live in London, UK. My bank card is from Lloyds Bank, card number is 1234123412331234.")
      .language("en")
      .build();
    List<AnalyzeResponse> jsonAnalysis = analyzerClient.analyze(req);
        /*
          Example Analysis Response:
          [
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "SpacyRecognizer_140736845369552",
                "recognizer_name" : "SpacyRecognizer"
              },
              "entity_type" : "PERSON",
              "score" : 0.85,
              "start" : 11,
              "end" : 28
            },
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "SpacyRecognizer_140736845369552",
                "recognizer_name" : "SpacyRecognizer"
              },
              "entity_type" : "LOCATION",
              "score" : 0.85,
              "start" : 40,
              "end" : 46
            },
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "SpacyRecognizer_140736845369552",
                "recognizer_name" : "SpacyRecognizer"
              },
              "entity_type" : "LOCATION",
              "score" : 0.85,
              "start" : 48,
              "end" : 50
            },
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "SpacyRecognizer_140736845369552",
                "recognizer_name" : "SpacyRecognizer"
              },
              "entity_type" : "DATE_TIME",
              "score" : 0.85,
              "start" : 101,
              "end" : 117
            },
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "UsBankRecognizer_140736852806384",
                "recognizer_name" : "UsBankRecognizer"
              },
              "entity_type" : "US_BANK_NUMBER",
              "score" : 0.4,
              "start" : 101,
              "end" : 117
            },
            {
              "analysis_explanation" : null,
              "recognition_metadata" : {
                "recognizer_identifier" : "UsLicenseRecognizer_140736852807056",
                "recognizer_name" : "UsLicenseRecognizer"
              },
              "entity_type" : "US_DRIVER_LICENSE",
              "score" : 0.01,
              "start" : 101,
              "end" : 117
            }
          ]
        */

    // Encrypt
    Map<String, AnonymizerStrategy> anonSettings = Map.of(
      "DEFAULT", new EncryptAnonymizerStrategy("1111111111111111")
    );
    AnonymizeResponse jsonEncrypt = anonymizerClient.anonymize(req.text(), jsonAnalysis, anonSettings);
        /*
          Example Anonymize Response:
          {
            "text" : "My name is 1f2/w/XBYso3c9WtVBY3tkhJUpYBgMWerSUuc18rNRqg/bD0ijpX08qe05kwCfpY, I live in yv1WvHTAMb5Vg7u1U+HRWduY4f8qCVgO4Tu0bTAOoUU=, bGa06OPm3tjlnC7ilWshrqA6Nx+42xGClcVv3JzNF3Y=. My bank card is from Llyods Bank, card number is cvQyTsEB79BgQwpxyoDHQN0xUAXszlr/nG2ep7Vp1mblFm28a8AIhYJ9Q/gxPtKK.",
            "items" : [
              {
                "operator" : "encrypt",
                "entity_type" : "DATE_TIME",
                "start" : 228,
                "end" : 292,
                "text" : "cvQyTsEB79BgQwpxyoDHQN0xUAXszlr/nG2ep7Vp1mblFm28a8AIhYJ9Q/gxPtKK"
              },
              {
                "operator" : "encrypt",
                "entity_type" : "LOCATION",
                "start" : 133,
                "end" : 177,
                "text" : "bGa06OPm3tjlnC7ilWshrqA6Nx+42xGClcVv3JzNF3Y="
              },
              {
                "operator" : "encrypt",
                "entity_type" : "LOCATION",
                "start" : 87,
                "end" : 131,
                "text" : "yv1WvHTAMb5Vg7u1U+HRWduY4f8qCVgO4Tu0bTAOoUU="
              },
              {
                "operator" : "encrypt",
                "entity_type" : "PERSON",
                "start" : 11,
                "end" : 75,
                "text" : "1f2/w/XBYso3c9WtVBY3tkhJUpYBgMWerSUuc18rNRqg/bD0ijpX08qe05kwCfpY"
              }
            ]
          }
        */

    // Decrypt
    Map<String, DecryptAnonymizer> decryptMap = Map.of(
      "DEFAULT", new DecryptAnonymizer("1111111111111111")
    );
    DeanonymizeResponse jsonDecrypt = anonymizerClient.deanonymize(
      new DeanonymizeRequest(jsonEncrypt.text(), decryptMap, jsonEncrypt.items())
    );
        /*
          Example Deanonymize Response:
          {
            "text" : "My name is Cameron Mukherjee, I live in London, UK. My bank card is from Llyods Bank, card number is 1234123412331234.",
            "items" : [
              {
                "operator" : "decrypt",
                "entity_type" : "DATE_TIME",
                "start" : 101,
                "end" : 117,
                "text" : "1234123412331234"
              },
              {
                "operator" : "decrypt",
                "entity_type" : "LOCATION",
                "start" : 48,
                "end" : 50,
                "text" : "UK"
              },
              {
                "operator" : "decrypt",
                "entity_type" : "LOCATION",
                "start" : 40,
                "end" : 46,
                "text" : "London"
              },
              {
                "operator" : "decrypt",
                "entity_type" : "PERSON",
                "start" : 11,
                "end" : 28,
                "text" : "Cameron Mukherjee"
              }
            ]
          }
        */
}
```

## Configuration

Ensure that the Presidio services are running and accessible at the specified URLs. You can configure the client details by providing the base URLs for the analyzer and anonymizer
services.

```java
PresidioAnalyzerClient analyzerClient = new PresidioAnalyzerClient(
  new PresidioAnalyzerClientDetails("http://localhost:5001")
);
PresidioAnonymizerClient anonymizerClient = new PresidioAnonymizerClient(
  new PresidioAnonymizerClientDetails("http://localhost:5002")
);
```

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a new branch: `git checkout -b feature/YourFeature`
3. Make your changes and commit them: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/YourFeature`
5. Open a pull request.

Please ensure that your code follows the project's coding standards and includes appropriate tests.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
