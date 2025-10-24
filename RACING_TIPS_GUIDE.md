# Racing Tips Notebook - User Guide

## Overview

**Racing Tips Notebook** is an AI-powered system for aggregating and analyzing horse racing tips from multiple sources. Built on the Open Notebook platform, it combines powerful AI capabilities with a user-friendly interface to help you make sense of racing tips data.

## Getting Started

### 1. Initial Setup

After deploying Racing Tips Notebook using Docker (see README.md), you'll need to:

1. **Set your AI API key** in the `docker.env` file:
   - Recommended: `ANTHROPIC_API_KEY` (Claude models excel at vision tasks)
   - Alternative: `OPENAI_API_KEY` (GPT-4 Vision also works well)
   - Alternative: `GOOGLE_API_KEY` (Gemini for long context)

2. **Start the container**:
   ```bash
   docker compose -f docker-compose.single.yml up -d
   ```

3. **Access the interface** at `http://localhost:8502`

### 2. Create Your First Racing Notebook

**Notebooks** are collections of racing tips organized by meeting, date, or any other criteria you choose.

1. Click **"Notebooks"** in the sidebar
2. Click **"+ New Notebook"**
3. Name it (e.g., "Flemington Saturday 2025-10-25")
4. Add a description (optional)

### 3. Upload Racing Tip Sheets

Racing Tips Notebook can extract tips from:
- Newspaper tip sheet images (JPG, PNG, TIFF)
- PDF racing guides
- Scanned documents
- Digital tip sheets

**To upload a tip sheet:**

1. Click **"Sources"** in the sidebar
2. Click **"+ Add Source"**
3. Select **"File Upload"**
4. Choose your tip sheet image or PDF
5. The AI will automatically:
   - Detect tipster columns
   - Extract race numbers
   - Identify horse names and positions
   - Parse selections for 1st, 2nd, 3rd places

### 4. Link Sources to Notebooks

After uploading a tip sheet:

1. Open the source detail page
2. Click **"Add to Notebook"**
3. Select the relevant notebook (e.g., "Flemington Saturday")
4. The tips are now organized in your notebook

### 5. Query Your Tips with Natural Language

The power of Racing Tips Notebook comes from its AI-powered search and chat capabilities.

**Example queries:**

- "Show me all tips for Flemington on Saturday"
- "Which horses have the most tips for 1st place in race 3?"
- "Compare tipsters for race 5"
- "What are the best bets for today?"
- "Which tipsters picked horse number 7?"
- "Show me the expert consensus for race 2"

**To query:**

1. Click **"Search"** in the sidebar
2. Type your natural language query
3. The AI will search across all your tips and provide relevant results
4. Results include source citations so you can verify the information

### 6. Chat with Your Racing Data

For more interactive analysis:

1. Open a **Notebook**
2. Click on the **Chat** tab
3. Ask questions about the tips in that notebook
4. The AI has full context of all tips in the notebook

**Example chat questions:**

- "What's the consensus pick for race 1?"
- "How many tipsters picked [horse name]?"
- "Which horses appear most frequently across all races?"
- "Are there any horses with strong support for a place?"
- "What patterns do you see in the tipsters' selections?"

### 7. Create AI-Generated Insights

Racing Tips Notebook can generate structured insights from your tips:

1. Open a **Source** (tip sheet)
2. Click **"Generate Insight"**
3. Choose a transformation type:
   - **Summary**: Overview of all tips
   - **Key Points**: Important selections and patterns
   - **Analysis**: Detailed breakdown by race
   - **Consensus Report**: Expert agreement analysis

The AI will analyze the tips and create a formatted report.

### 8. Advanced: Custom Transformations

You can create custom AI transformations for racing-specific analysis:

1. Go to **"Transformations"** in the sidebar
2. Click **"+ New Transformation"**
3. Name it (e.g., "Race 1 Consensus Analysis")
4. Write a prompt template:
   ```
   Analyze the racing tips for Race 1 and provide:
   1. Which horse has the most tips for 1st place
   2. The consensus for place positions (2nd, 3rd)
   3. Any notable patterns or agreements among tipsters
   4. Horses that appear frequently but not for 1st place
   ```
5. Apply this transformation to any source

## Workflow Examples

### Workflow 1: Weekly Racing Tips Aggregation

1. **Monday**: Create notebook "Weekend Racing - Oct 25-26"
2. **Tuesday-Friday**: Upload tip sheets as they're published
   - Herald Sun tips
   - Racing Post tips
   - Form guides
   - Expert selections
3. **Saturday morning**: 
   - Chat with the notebook: "What are the consensus picks for today?"
   - Generate a summary report
   - Review horses with strong tipster support
4. **Sunday**: Add actual results and compare

### Workflow 2: Tipster Performance Tracking

1. Create notebooks for each race meeting
2. Upload all tipster selections
3. After the races, add results as notes
4. Use search to query: "Show me all tips from [tipster name]"
5. Compare their picks against actual results
6. Track accuracy over time

### Workflow 3: Form Guide Analysis

1. Upload racing form guides (PDFs or images)
2. Upload tipster selections
3. Create a notebook combining both sources
4. Chat: "Compare the form guide recommendations with tipster consensus"
5. Generate insights about agreement/disagreement
6. Identify value picks where form differs from tips

## Tips for Best Results

### OCR Extraction Tips

1. **Image Quality**: Higher resolution images produce better OCR results
2. **Lighting**: Ensure tip sheets are well-lit and not shadowed
3. **Orientation**: Upload images in correct orientation (not rotated)
4. **Format**: PNG or JPEG work best; TIFF is also supported
5. **Multi-page**: For multi-page documents, upload as a single PDF if possible

### Organizing Your Data

1. **Consistent Naming**: Use consistent notebook names (e.g., "Venue - Date")
2. **Tag Sources**: Add descriptive titles to sources (e.g., "Herald Sun - Flemington - Oct 25")
3. **Regular Cleanup**: Archive old notebooks to keep the interface clean
4. **Separate by Meeting**: Create one notebook per race meeting for clarity

### Querying Tips

1. **Be Specific**: Include venue, date, or race number in queries
2. **Use Context**: Chat within a notebook for meeting-specific questions
3. **Iterate**: If results aren't perfect, rephrase your question
4. **Cite Sources**: Always check the source citations in results

## Troubleshooting

### OCR Not Working

**Problem**: Uploaded tip sheet but no tips extracted

**Solutions**:
- Verify your `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` is set correctly
- Check image quality (should be clear and readable)
- Try uploading as PDF instead of image
- Ensure the tip sheet has a standard columnar format

### Poor Extraction Quality

**Problem**: Tips extracted but many errors or missing data

**Solutions**:
- Use higher resolution images (300 DPI or higher)
- Ensure text is clear and not blurred
- Try a different AI provider (switch between Anthropic/OpenAI/Gemini)
- For complex layouts, consider manual data entry

### Search Not Finding Tips

**Problem**: Search returns no results even though tips exist

**Solutions**:
- Wait a few minutes for embeddings to be generated
- Check that sources are linked to notebooks
- Try more general search terms
- Verify the tips were successfully extracted (check source detail page)

### Slow Performance

**Problem**: AI responses are slow or timing out

**Solutions**:
- Increase `API_CLIENT_TIMEOUT` in docker.env (try 600 seconds)
- If using Ollama locally, ensure GPU acceleration is enabled
- Consider using cloud AI providers (Anthropic/OpenAI) for faster responses
- Reduce the number of sources in a single notebook

## Data Privacy

Racing Tips Notebook is designed with privacy in mind:

- **Self-hosted**: All data stays on your server
- **No external storage**: Tips are stored in your local SurrealDB instance
- **API key control**: You control which AI providers see your data
- **Local AI option**: Use Ollama for 100% local processing (no cloud APIs)

## Advanced Features

### API Access

Racing Tips Notebook includes a full REST API:

- **Documentation**: http://localhost:5055/docs
- **Endpoints**: Create notebooks, upload sources, query tips programmatically
- **Automation**: Build scripts to automatically upload daily tip sheets

### Podcast Generation (Optional)

Generate audio podcasts discussing racing tips:

1. Go to **"Podcasts"** in the sidebar
2. Select sources (tip sheets)
3. Configure speakers (racing experts)
4. Generate a conversational podcast about the tips
5. Listen to AI-generated racing analysis

### Custom AI Models

Racing Tips Notebook supports 16+ AI providers:

- **Anthropic**: Claude models (excellent for vision/OCR)
- **OpenAI**: GPT-4 Vision (strong all-around performance)
- **Google**: Gemini (best for long documents)
- **Ollama**: Run models locally (privacy-focused)
- **LM Studio**: Self-hosted models
- **And more**: Groq, Mistral, DeepSeek, etc.

Configure in **Settings** → **Models**

## Support and Community

- **GitHub Issues**: https://github.com/domocarroll/racing-tips-notebook/issues
- **Original Project**: Built on [Open Notebook](https://github.com/lfnovo/open-notebook)
- **Discord**: Join the Open Notebook Discord for general platform support

## Next Steps

1. **Upload your first tip sheet** and see the AI extraction in action
2. **Create notebooks** for upcoming race meetings
3. **Experiment with queries** to find useful patterns
4. **Track tipster performance** over time
5. **Share insights** with your racing community

Happy racing! 🏇

