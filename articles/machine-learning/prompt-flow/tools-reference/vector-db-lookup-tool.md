---
title: Vector DB Lookup tool in Azure Machine Learning prompt flow (preview)
titleSuffix: Azure Machine Learning
description: Vector DB Lookup is a vector search tool that allows users to search top k similar vectors from vector database. This tool is a wrapper for multiple third-party vector databases. The list of current supported databases is as follows.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.reviewer: lagayhar
ms.date: 06/30/2023
---

# Vector DB Lookup tool (preview)

Vector DB Lookup is a vector search tool that allows users to search top k similar vectors from vector database. This tool is a wrapper for multiple third-party vector databases. The list of current supported databases is as follows.

| Name | Description |
| --- | --- |
| Azure Cognitive Search | Microsoft's cloud search service with built-in AI capabilities that enrich all types of information to help identify and explore relevant content at scale. |

This tool adds support for more vector databases, including Pinecone, Weaviete, Qdrant etc.

> [!IMPORTANT]
> Prompt flow is currently in public preview. This preview is provided without a service-level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Requirements

- embeddingstore --extra-index-url https://azuremlsdktestpypi.azureedge.net/embeddingstore

## Prerequisites

The tool searches data from a third-party vector database. To use it, you should create resources in advance and establish connections between the tool and the resource.

  - **Azure Cognitive Search:**
    - Create resource [Azure Cognitive Search](../../../search/search-create-service-portal.md).
    - Add "CognitiveSearchConnection" connection. Fill "API key" field with "Primary admin key" from "Keys" section of created resource, and fill "Api Base" field with the URL, the URL format is `https://{your_serive_name}.search.windows.net`.

## Inputs

The following are available input parameters:
- **Azure Cognitive Search:**

  | Name | Type | Description | Required |
  | ---- | ---- | ----------- | -------- |
  | connection | CognitiveSearchConnection | The created workspace connection for accessing to Cognitive Search endpoint. | Yes |
  | index_name | string | The index name created in Cognitive Search resource. | Yes |
  | text_field | string | The text field name. The returned text filed will populate the result of text. | No |
  | vector_field | string | The vector field name. The target vector is searched in this vector field. | Yes |
  | search_params | dict | The search parameters. It's key-value pairs. Except for parameters in the tool input list mentioned above, additional search parameters can be formed into a JSON object as search_params. For example, use `{"select": ""}` as search_params to select the returned fields, use `{"search": "", "queryType": "", ""semanticConfiguration": "", "queryLanguage": ""}` to perform a hybrid search. | No |
  | search_filters | dict | The search filters. It's key-value pairs, the input format is like `{"filter": ""}` | No |
  | vector | list | The target vector to be queried, which can be generated by the LLM tool. | Yes |
  | top_k | int | The count of top-scored entities to return. Default value is 3 | No |


## Outputs

The following is an example JSON format response returned by the tool, which includes the top-k scored entities. The entity follows a generic schema of vector search result provided by our EmbeddingStore SDK.

**Azure Cognitive Search:**

  For the Azure Cognitive Search, the following fields are populated:

| Field Name      | Type   | Description                                                       |
|-----------------|--------|-------------------------------------------------------------------|
| vector          | list   | vector of the entity, the vector field name is specified in input |
| text            | string | text of the entity, the text field name is specified in input     |
| score           | float  | computed by the BM25 similarity algorithm                         |
| original_entity | dict   | the original response json from search REST API                   |


  ```json
  [
    {
      "metadata": null,
      "original_entity": {
        "@search.score": 0.5099789,
        "id": "",
        "your_text_filed_name": "text",
        "your_vector_filed_name": [-0.40517663431890405, 0.5856996257406859, -0.1593078462266455, -0.9776269170785785, -0.6145604369828972],
        "your_additional_field_name": ""
      },
      "score": 0.5099789,
      "text": "text",
      "vector": [-0.40517663431890405, 0.5856996257406859, -0.1593078462266455, -0.9776269170785785, -0.6145604369828972]
    }
  ]
  ```
