# RollBounce WebGL Deployment

This repository contains the WebGL build of RollBounce for deployment to test.vonbase.com via Render.

## Quick Start

### Prerequisites
- Unity 2021.3+ with WebGL Build Support installed
- Git
- GitHub CLI (`gh`) for repo creation

### Building and Deploying

#### 1. Build WebGL in Unity

**CRITICAL**: Configure Unity build settings to avoid browser errors:

1. Open `/mnt/c/Users/Tyvon/RollBouncev1` in Unity Editor

2. **File → Build Settings → WebGL**

3. **Player Settings → Publishing Settings**:
   - **Compression Format**: Set to **Disabled** (recommended)
     - OR enable **Decompression Fallback** if using Gzip/Brotli
   - This prevents MIME type and Content-Encoding errors in browsers

4. **Player Settings → Resolution and Presentation**:
   - Default Canvas Width: 1920
   - Default Canvas Height: 1080
   - Run In Background: ✅ Enabled

5. **Build**:
   - Click "Build" (NOT "Build and Run")
   - Output directory: `/mnt/c/Users/Tyvon/Dev/rollbounce-webgl-build`

#### 2. Copy Build to Deployment Repo

```bash
cd ~/dev/rollbounce-webgl-deploy

# Remove old build files
rm -rf Build TemplateData index.html

# Copy new build
cp -r /mnt/c/Users/Tyvon/Dev/rollbounce-webgl-build/* .
```

#### 3. Commit and Push

```bash
git add .
git commit -m "Update WebGL build - $(date +%Y-%m-%d)"
git push origin main
```

Render will automatically deploy from GitHub within 1-2 minutes.

## Repository Structure

```
rollbounce-webgl-deploy/
├── Build/                    # Unity WebGL build artifacts
│   ├── rollbounce.loader.js
│   ├── rollbounce.framework.js
│   ├── rollbounce.data
│   └── rollbounce.wasm
├── TemplateData/             # UI assets
│   ├── style.css
│   ├── favicon.ico
│   └── ...
├── index.html                # Main entry point
├── render.yaml               # Render configuration
├── .gitignore
└── README.md
```

## Render Configuration

The `render.yaml` file configures proper MIME types and headers:

- **Content-Type**: `application/wasm` for .wasm files
- **Content-Type**: `application/javascript` for .js files
- **Content-Encoding**: `gzip` and `br` for compressed builds
- **Cache-Control**: Optimized for static assets

## Deployment URL

- **Production**: https://test.vonbase.com
- **Render Dashboard**: https://dashboard.render.com

## Troubleshooting

### Game Won't Load in Browser

**Symptom**: Console errors like "MIME type mismatch" or "Unable to parse Build"

**Solutions**:
1. **Rebuild with Decompression Fallback**:
   - Unity → Publishing Settings → Enable "Decompression Fallback"
   - Rebuild WebGL

2. **Use Uncompressed Build**:
   - Unity → Publishing Settings → Compression Format: Disabled
   - Rebuild WebGL

3. **Check Browser Console** (F12):
   - Look for specific error messages
   - Verify .wasm files load with correct Content-Type

### Deployment Not Updating

1. Check Render Dashboard for build logs
2. Verify GitHub webhook is configured
3. Manually trigger deploy from Render Dashboard

### Performance Issues

1. **Enable Compression** (if disabled):
   - Gzip/Brotli reduce file sizes by ~70%
   - Requires proper Content-Encoding headers

2. **Optimize Unity Build**:
   - Player Settings → Strip Engine Code: ✅
   - Player Settings → Code Optimization: Size
   - Reduce texture quality in Import Settings

## Unity Build Settings Summary

| Setting | Recommended Value | Why |
|---------|------------------|-----|
| **Compression Format** | Disabled or enable Fallback | Prevents browser errors |
| **Decompression Fallback** | ✅ Enabled (if compressed) | JavaScript decompression backup |
| **Strip Engine Code** | ✅ Enabled | Reduces file size |
| **Code Optimization** | Size | Smaller .wasm files |
| **Run In Background** | ✅ Enabled | Better UX |

## Source Code

Unity project source: https://github.com/Tyvonneboykin/RollBouncev1

## Support

For issues with:
- **Unity Build**: Check RollBouncev1 repo issues
- **Deployment**: Check Render Dashboard logs
- **Domain/DNS**: Contact Vonbase infrastructure team
