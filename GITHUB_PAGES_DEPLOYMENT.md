# GitHub Pages Deployment Guide

This guide explains how to deploy the TTUCoLab project to GitHub Pages for static hosting with client-side AI recommendations.

## Overview

The project has been adapted to run entirely in the browser using:
- **ONNX Runtime Web** for client-side ML inference
- **D3.js** for data visualization  
- **Static file serving** via GitHub Pages

## Prerequisites

Before deployment, you need to:

1. **Convert models to ONNX format** (one-time setup)
2. **Push files to GitHub**
3. **Enable GitHub Pages** in repository settings

## Step 1: Convert Models to ONNX

The Python `.pkl` models must be converted to `.onnx` format for browser usage.

### Install Requirements

```bash
pip install skl2onnx onnx onnxruntime scikit-learn numpy pandas
```

### Run Conversion Script

```bash
python convert_models_to_onnx.py
```

This will create:
- `model_artifacts/tfidf_vectorizer.onnx`
- `model_artifacts/logistic_model.onnx`
- `model_artifacts/label_encoder.json`

### Verify Files

Check that the ONNX files were created:

```bash
ls -la model_artifacts/
```

You should see:
```
tfidf_vectorizer.onnx
logistic_model.onnx
label_encoder.json
tfidf_vectorizer.pkl  (not needed for deployment)
logistic_model.pkl     (not needed for deployment)
label_encoder.pkl      (not needed for deployment)
```

## Step 2: Prepare for GitHub

### Commit ONNX Models

**Important:** The `.onnx` files MUST be committed to the repository!

```bash
git add model_artifacts/*.onnx
git add model_artifacts/label_encoder.json
git add .github/workflows/deploy.yml
git commit -m "Add ONNX models for GitHub Pages deployment"
```

### Push to GitHub

```bash
git push origin main
```

## Step 3: Enable GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings** → **Pages** (left sidebar)
3. Under **Source**, select:
   - Source: **GitHub Actions**
4. The site will automatically deploy via the GitHub Actions workflow

## Step 4: Access Your Site

Your site will be available at:
```
https://<username>.github.io/<repository-name>/index.html
```

For example:
```
https://myusername.github.io/TTUCoLab/index.html
```

## Features on GitHub Pages

### ✅ Available Features

- **Data Visualization**: All D3.js visualizations work
- **AI Recommendations**: Client-side theme prediction and researcher recommendations
- **Browse Research**: View existing grants and publications
- **Theme Analysis**: Real-time ML inference in the browser

### ⚠️ Limited Features

- **Add Project**: Can only update local view (not persisted)
- **Data Updates**: To add new data, edit `grants_final.tsv` and push to GitHub

## Architecture

```
User's Browser
    ↓
GitHub Pages (Static Files)
    ↓
┌─────────────────────────────────────┐
│ index.html                          │
│ ├─ ONNX Runtime Web (ML inference)  │
│ ├─ D3.js (visualization)            │
│ └─ grants_final.tsv (data)          │
└─────────────────────────────────────┘
```

## Troubleshooting

### Models Not Loading

**Error:** "Failed to load model_artifacts/logistic_model.onnx"

**Solution:**
1. Verify ONNX files are committed: `git ls-files model_artifacts/`
2. Check browser console for CORS errors
3. Ensure files are not in `.gitignore`

### Recommendations Not Working

**Error:** "Recommendation engine not initialized"

**Solution:**
1. Open browser DevTools (F12) → Console
2. Check for error messages
3. Verify ONNX Runtime is loaded: `typeof ort !== 'undefined'`
4. Ensure models are accessible via your GitHub Pages URL

### 404 Errors

**Error:** "404 Not Found" for resources

**Solution:**
1. Check that paths are relative (no leading `/`)
2. Verify `grants_final.tsv` is in root directory
3. Ensure GitHub Actions workflow completed successfully

### Performance Issues

**Issue:** Slow initial load

**Explanation:** ONNX models are downloaded on first page load (~5-10 MB total)

**Solutions:**
- Models are cached after first load
- Consider adding a loading indicator
- Compress models if possible

## Updating Data

To add new research projects:

1. Edit `grants_final.tsv` locally
2. Follow the TSV format:
   ```
   "Code"\tTime\t"Theme"\t'Title'\t"Authors"
   ```
3. Commit and push:
   ```bash
   git add grants_final.tsv
   git commit -m "Update research data"
   git push origin main
   ```
4. GitHub Pages will automatically redeploy

## File Structure for Deployment

### Required Files
```
index.html                           # Main page
grants_final.tsv                     # Data file
model_artifacts/
  ├── tfidf_vectorizer.onnx         # ✅ Required
  ├── logistic_model.onnx            # ✅ Required
  └── label_encoder.json             # ✅ Required
pubJavascripts/
  ├── javascripts/
  │   ├── d3.v3.min.js
  │   └── fisheye.js
  └── myscripts/
      ├── main.js
      ├── recommendation-engine.js   # ✅ Required
      └── util.js
  └── styles/
      └── timeArcs.css
.github/workflows/deploy.yml         # ✅ Required for auto-deployment
```

### Optional Files (Not Deployed)
```
server.py                           # Local dev only
recommend_researchers.py            # Local dev only
train_model.ipynb                   # Local dev only
*.pkl files                         # Python models (converted to ONNX)
```

## Comparing Local vs GitHub Pages

| Feature | Local Server | GitHub Pages |
|---------|-------------|--------------|
| **Hosting** | Python server.py | Static GitHub Pages |
| **ML Inference** | Server-side (Python) | Client-side (ONNX.js) |
| **Add Project** | Saves to TSV file | Local view only |
| **Performance** | Fast (local) | Dependent on network |
| **Requirements** | Python + dependencies | Just a browser |
| **Cost** | Free (self-hosted) | Free (GitHub) |

## Security Notes

- All data in `grants_final.tsv` is publicly accessible
- Don't commit sensitive information
- `.pkl` files contain model weights only (not data)

## Advanced: Custom Domain

To use a custom domain:

1. Add a `CNAME` file to repository root:
   ```
   www.yourdomain.com
   ```
2. Configure DNS with your domain provider
3. In GitHub Settings → Pages, set custom domain

## Support

For issues:
1. Check browser console (F12)
2. Review GitHub Actions logs
3. Ensure all ONNX files are committed
4. Test locally first with a simple HTTP server:
   ```bash
   python -m http.server 8080
   ```

## Next Steps

- ✅ Convert models to ONNX
- ✅ Push to GitHub
- ✅ Enable GitHub Pages
- 📊 Monitor site performance
- 🎨 Customize styling
- 📱 Add mobile responsiveness
- 🔍 Improve search functionality

---

**Congratulations!** Your research recommendation system is now live on GitHub Pages! 🎉
