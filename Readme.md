# ShopWise Solutions AI Customer Support

## Setup and Testing Guide

This guide provides detailed instructions for setting up and testing the ShopWise Solutions AI-powered customer support system implemented using n8n workflows.

### Table of Contents

- [Prerequisites](#prerequisites)
- [Repository Structure](#repository-structure)
- [Environment Setup](#environment-setup)
- [Workflow Installation](#workflow-installation)
- [Configuration Steps](#configuration-steps)
- [Testing the System](#testing-the-system)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

### Prerequisites

1. **n8n Installation**

   - n8n version 1.0.0 or higher
   - Installation options:

     ```bash
     # Using npm
     npm install n8n -g

     # Using Docker
     docker pull n8nio/n8n
     docker run -it --rm \
       --name n8n \
       -p 5678:5678 \
       n8nio/n8n
     ```

2. **Required API Keys**

   - OpenAI API key
   - Pinecone API key
   - Pinecone Environment

3. **System Requirements**
   - Node.js 16.x or higher
   - 2GB RAM minimum
   - 10GB available storage

### Repository Structure

```
shopwise-ai-support/
├── workflows/
│   ├── knowledge_base_sync.json    # Knowledge base synchronization workflow
│   ├── chat_support.json           # Customer support chat workflow
├── sample_data/
│   ├── synthetic-product-data.csv                # Sample product data for testing
│   ├── synthetic-oders-data.csv                  # Sample order data for testing
├── configs/
│   ├── pinecone_config.json        # Pinecone configuration template
│   ├── openai_config.json          # OpenAI configuration template
└── README.md                       # This file
```

### Environment Setup

1. **Clone the Repository**

   ```bash
   git clone https://github.com/shopwise/ai-support-system.git
   cd ai-support-system
   ```

2. **Set up Environment Variables**
   Create a `.env` file in your n8n root directory:
   ```env
   OPENAI_API_KEY=your_openai_api_key
   PINECONE_API_KEY=your_pinecone_api_key
   PINECONE_ENVIRONMENT=your_pinecone_environment
   PINECONE_INDEX_NAME=shopwise-products
   N8N_ENCRYPTION_KEY=your_encryption_key
   ```

### Workflow Installation

1. **Import Knowledge Base Sync Workflow**

   - Open n8n UI (default: http://localhost:5678)
   - Navigate to Workflows
   - Click "Import from File"
   - Select `knowledge_base_sync.json`
   - Save the workflow

2. **Import Chat Support Workflow**
   - Repeat the above steps for `chat_support.json`
   - Both workflows should now appear in your n8n dashboard

### Configuration Steps

1. **Configure Pinecone Connection**

   ```javascript
   // In Pinecone configuration node:
   {
     "apiKey": "{{$env.PINECONE_API_KEY}}",
     "environment": "{{$env.PINECONE_ENVIRONMENT}}",
     "indexName": "{{$env.PINECONE_INDEX_NAME}}"
   }
   ```

2. **Configure OpenAI Settings**

   ```javascript
   // In OpenAI configuration node:
   {
     "apiKey": "{{$env.OPENAI_API_KEY}}",
     "model": "gpt-4o",
     "temperature": 0.7,
     "maxTokens": 500
   }
   ```

3. **Set Up Data Sources**
   - Import sample data files from `sample_data/` directory
   - Configure CSV Reader nodes to point to these files
   - Verify data paths in both workflows

### Testing the System

1. **Test Knowledge Base Sync Workflow**

   ```bash
   # Execute via n8n CLI
   n8n execute --workflow "Knowledge Base Sync"

   # Or use the UI:
   1. Open the workflow
   2. Click "Execute Workflow"
   3. Check execution results in the debug panel
   ```

   Expected outcomes:

   - Successful data extraction from CSV files
   - Vector embeddings generation
   - Pinecone index update
   - Execution completion message

2. **Test Chat Support Workflow**

   ```bash
   # Test queries through the chat interface:
   1. Access the chat endpoint (provided in UI)
   2. Send test queries:
      - "What are your return policies?"
      - "Tell me about product XYZ"
      - "How can I track my order?"
   ```

   Expected responses:

   - Contextually relevant answers
   - Product-specific information
   - Policy details

3. **Validation Steps**

   - Verify vector storage:

     ```bash
     # Check Pinecone index stats
     curl -X GET \
       -H "Api-Key: ${PINECONE_API_KEY}" \
       "https://${INDEX_NAME}-${PROJECT_ID}.svc.${ENVIRONMENT}.pinecone.io/describe_index_stats"
     ```

   - Verify embeddings:
     ```javascript
     // In n8n Function node:
     const testQuery = "test product";
     const embedding = await getEmbedding(testQuery);
     console.log("Embedding generated:", embedding.length);
     ```

### Troubleshooting

1. **Common Issues**

   a) Knowledge Base Sync Failures

   ```
   Issue: "Error: Failed to connect to Pinecone"
   Solution: Verify Pinecone credentials and environment variables
   ```

   b) Chat Response Delays

   ```
   Issue: "Timeout waiting for response"
   Solution: Check OpenAI API limits and connection settings
   ```

   c) Vector Storage Issues

   ```
   Issue: "Index not found"
   Solution: Verify Pinecone index creation and naming
   ```

2. **Debug Mode**
   - Enable debug mode in n8n:
     ```bash
     n8n start --debug
     ```
   - Check logs at `~/.n8n/logs/`

### Additional Resources

1. **API Documentation**

   - [n8n Documentation](https://docs.n8n.io)
   - [Pinecone API Reference](https://docs.pinecone.io/reference)
   - [OpenAI API Documentation](https://platform.openai.com/docs)

2. **Support Channels**

   - GitHub Issues: [Report bugs here](https://github.com/shopwise/ai-support-system/issues)
   - n8n Community Forum: [Ask questions](https://community.n8n.io)

3. **Sample Queries for Testing**

   ```json
   {
     "basic_queries": [
       "What's the difference between Product A and Product B?"
     ],
     "product_specific": [
       "I see there are some Sony TVs in your catalog. Can you compare the features and prices between the Sony KD75XF8596BU and other TV models you have?"
     ],
     "complex_scenarios": [
       "I notice you have mobile phones and digital cameras. For someone interested in photography, would you recommend the Sony peria XA2 Ultra or the Pentax K-1 camera? Please explain the pros and cons of each for photography."
     ]
   }
   ```

### Version Information

- n8n Version: 1.68.0
- Workflow Versions:
  - Knowledge Base Sync: v1.0.0
  - Chat Support: v1.0.0
- Last Updated: November 2024
