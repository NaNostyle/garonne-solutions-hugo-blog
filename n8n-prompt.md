use n8n-mcp to create a workflow that will :
take a When chat message received node as entry
use my n8n credentials openai account to make a new blog post to my hugo blog
use my huggin face account to make multiple requests to huggin face to generate a header image for the blog post and more image to illustrate the blog post content.
Then update the hugo blog with the new post
knowing that the hugo blog runs on vercel and my n8n instance on my local machine
the repo lives here /Users/nano/Documents/coding-projects/hugo-blog/garonne-solutions or here https://github.com/NaNostyle/garonne-solutions-hugo-blog i prefer using git
you cannot use terminal or modules

{
  "nodes": [
    {
      "parameters": {
        "method": "POST",
        "url": "https://api-inference.huggingface.co/models/black-forest-labs/FLUX.1-dev",
        "authentication": "predefinedCredentialType",
        "nodeCredentialType": "huggingFaceApi",
        "sendHeaders": true,
        "headerParameters": {
          "parameters": [
            {
              "name": "accept",
              "value": "image/png"
            }
          ]
        },
        "sendBody": true,
        "bodyParameters": {
          "parameters": [
            {
              "name": "inputs",
              "value": "={{ $json.chatInput }}"
            }
          ]
        },
        "options": {
          "response": {
            "response": {
              "responseFormat": "file"
            }
          }
        }
      },
      "id": "97dbd0bd-3715-4097-a06a-41a749badcc6",
      "name": "Generate Image with FLUX.1-dev",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [
        656,
        -80
      ],
      "credentials": {
        "huggingFaceApi": {
          "id": "LX5vskJDTMwIIKGX",
          "name": "HuggingFaceApi account"
        }
      }
    },
    {
      "parameters": {
        "messages": {
          "messageValues": [
            {},
            {
              "type": "HumanMessagePromptTemplate"
            }
          ]
        },
        "batching": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chainLlm",
      "typeVersion": 1.7,
      "position": [
        400,
        -304
      ],
      "id": "d82700c4-1dea-4538-85b2-3c95ed39c35f",
      "name": "Basic LLM Chain"
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "value": "gpt-4.1-mini",
          "mode": "list",
          "cachedResultName": "gpt-4.1-mini"
        },
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.2,
      "position": [
        400,
        -96
      ],
      "id": "4d032273-ff89-43be-8385-a094d0de69d3",
      "name": "OpenAI Chat Model",
      "credentials": {
        "openAiApi": {
          "id": "rElxvgVp9y8ieyVT",
          "name": "OpenAi account"
        }
      }
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [
        0,
        0
      ],
      "id": "3c9bbc22-2a91-4870-9137-92cce1b24d59",
      "name": "When clicking ‘Execute workflow’"
    },
    {
      "parameters": {
        "repositoryPath": "/Users/nano/Documents/coding-projects/hugo-blog/garonne-solutions/.git",
        "returnAll": true,
        "options": {}
      },
      "type": "n8n-nodes-base.git",
      "typeVersion": 1,
      "position": [
        208,
        0
      ],
      "id": "1249732c-b3c4-4b10-a620-74e0496a3a74",
      "name": "Git"
    }
  ],
  "connections": {
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "Basic LLM Chain",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "When clicking ‘Execute workflow’": {
      "main": [
        [
          {
            "node": "Git",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "pinData": {},
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "d95527dccd518d3219b103ca2ff85f7af0173e87e41d3b255b4a1849f5157ea1"
  }
}