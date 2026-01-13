# Hero Image Automation Guide for WebFoundryAI

Automated hero image generation and deployment pipeline using Claude Code + MCP servers.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        CLAUDE CODE ENVIRONMENT                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐         │
│  │  Leonardo AI    │    │  Cloudflare R2  │    │  GitHub         │         │
│  │  MCP Server     │───▶│  MCP Server     │───▶│  (Push CSS)     │         │
│  │                 │    │                 │    │                 │         │
│  │  Generate .webp │    │  Upload images  │    │  Auto-deploy    │         │
│  │  hero images    │    │  Get public URL │    │  via CF Pages   │         │
│  └─────────────────┘    └─────────────────┘    └─────────────────┘         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 1. MCP Server Setup

### A. Leonardo AI MCP Server

**Installation (Claude Code CLI):**
```bash
claude mcp add leonardo-mcp-server \
  --command uvx \
  --args "--from" "git+https://github.com/ish-joshi/leonardo-mcp-server" "leonardo-mcp-server" "stdio" \
  --env LEONARDO_API_KEY=your_api_key_here
```

**Or via JSON config (~/.claude/settings.json):**
```json
{
  "mcpServers": {
    "leonardo-mcp-server": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/ish-joshi/leonardo-mcp-server",
        "leonardo-mcp-server",
        "stdio"
      ],
      "env": {
        "LEONARDO_API_KEY": "YOUR_LEONARDO_API_KEY"
      }
    }
  }
}
```

**Get API Key:**
1. Go to https://leonardo.ai
2. Navigate to API settings
3. Generate a "Production API Key"

**Available Tools:**
- `generate_image` - Create images with prompts
- `list_models` - View available AI models
- `check_job_status` - Monitor generation progress
- `get_user_jobs` - Retrieve generation history

---

### B. Cloudflare MCP Server (includes R2)

**Installation (Claude Code CLI):**
```bash
claude mcp add cloudflare \
  --command npx \
  --args "-y" "@cloudflare/mcp-server-cloudflare"
```

**Or via JSON config:**
```json
{
  "mcpServers": {
    "cloudflare": {
      "command": "npx",
      "args": ["-y", "@cloudflare/mcp-server-cloudflare"],
      "env": {
        "CLOUDFLARE_ACCOUNT_ID": "your_account_id",
        "CLOUDFLARE_API_TOKEN": "your_api_token"
      }
    }
  }
}
```

**Get Cloudflare Credentials:**
1. Go to https://dash.cloudflare.com
2. Account ID: Found in the right sidebar of any zone
3. API Token: My Profile → API Tokens → Create Token
   - Use template: "Edit Cloudflare Workers" (includes R2 permissions)
   - Or custom with: Account.Workers R2 Storage:Edit

**Available R2 Tools:**
- `r2_list_buckets` - List all R2 buckets
- `r2_create_bucket` - Create new bucket
- `r2_list_objects` - List objects in bucket
- `r2_put_object` - Upload file to bucket
- `r2_get_object` - Download file from bucket
- `r2_delete_object` - Remove file from bucket

---

### C. Alternative: Image Worker MCP (Multi-cloud)

If you need flexibility across cloud providers:

```bash
npm install -g @boomlinkai/image-worker-mcp
```

Supports: AWS S3, Cloudflare R2, Google Cloud Storage

---

## 2. Cloudflare R2 Setup

### Create R2 Bucket

**Via Cloudflare Dashboard:**
1. Go to R2 Object Storage in dashboard
2. Create bucket: `webfoundry-images` (or per-client buckets)
3. Settings → Public Access → Enable
4. Copy public URL: `https://pub-xxxx.r2.dev`

**Via MCP (once server is configured):**
```
Create an R2 bucket named "webfoundry-images"
```

### Custom Domain (Recommended)

1. In R2 bucket settings → Custom Domains
2. Add: `images.yourdomain.com`
3. Cloudflare handles SSL automatically

### Folder Structure in R2

```
webfoundry-images/
├── bath-plumbers.co.uk/
│   ├── hero-home.webp
│   ├── hero-services.webp
│   ├── hero-about.webp
│   └── hero-contact.webp
├── another-client.com/
│   └── ...
```

---

## 3. WebP Image Specifications

### Recommended Settings

| Property | Value | Notes |
|----------|-------|-------|
| Format | WebP | 25-35% smaller than JPEG |
| Width | 1920px | Full HD desktop |
| Height | 600-800px | Landscape hero ratio |
| Quality | 80-85 | Good balance |
| Aspect Ratio | 3:1 or 2.4:1 | Wide landscape |

### Leonardo AI Prompt Templates

**Homepage Hero (Plumber):**
```
Professional plumber working on modern copper pipes, warm workshop lighting,
photorealistic, high quality, landscape orientation, commercial photography style
```

**Homepage Hero (Location-based):**
```
Aerial view of Bath city UK, Georgian architecture, Royal Crescent visible,
golden hour lighting, professional photography, landscape wide shot
```

**Service Page Hero:**
```
Close-up of professional plumbing tools and copper fittings,
clean workshop background, warm lighting, commercial product photography
```

**Emergency Services Hero:**
```
Professional emergency plumber responding to call,
van with equipment, residential street, dramatic lighting, photorealistic
```

### WebP Conversion (if Leonardo outputs PNG/JPG)

**Option A: Cloudflare Auto-Conversion**
- Enable "Polish" in Cloudflare dashboard (Pro plan)
- Or use Image Transformations (5,000 free/month)

**Option B: Local Conversion**
```bash
# Install cwebp
apt-get install webp  # Linux
brew install webp     # macOS

# Convert
cwebp -q 85 input.png -o output.webp
```

**Option C: In Pipeline**
Use sharp (Node.js) or Pillow (Python) for programmatic conversion.

---

## 4. CSS Integration

### Hero Image CSS Classes

The following CSS classes are available in `assets/css/style.css`:

```css
/* Homepage hero with background image */
.hero--with-image {
    background-image: url('https://your-r2-bucket.r2.dev/site/hero-home.webp');
    background-size: cover;
    background-position: center;
}

/* Page-specific heroes */
.page-hero--services { background-image: url('...hero-services.webp'); }
.page-hero--about { background-image: url('...hero-about.webp'); }
.page-hero--contact { background-image: url('...hero-contact.webp'); }
```

### URL Pattern

```
https://[bucket].r2.dev/[site-folder]/[image-name].webp
```

Or with custom domain:
```
https://images.webfoundryai.com/[site-folder]/[image-name].webp
```

---

## 5. Automated Workflow

### Full Pipeline Prompt for Claude Code

```
Generate hero images for bath-plumbers.co.uk:

1. Use Leonardo AI to generate:
   - Homepage hero: "Professional plumber in Bath, Georgian architecture background"
   - Services hero: "Plumbing tools and equipment, professional photography"
   - About hero: "Bath city skyline, historic buildings"
   - Contact hero: "Friendly plumber with customer, residential setting"

2. Upload all images to R2 bucket: webfoundry-images/bath-plumbers.co.uk/

3. Update style.css with the R2 URLs

4. Commit and push changes
```

### Per-Website Checklist

- [ ] Generate 4-6 hero images via Leonardo AI
- [ ] Convert to WebP if needed
- [ ] Upload to R2: `bucket/site-name/`
- [ ] Update CSS with R2 URLs
- [ ] Test responsive behavior
- [ ] Commit and deploy

---

## 6. Cost Summary

| Service | Free Tier | Paid Rate |
|---------|-----------|-----------|
| Leonardo AI | 150 credits/day | API pricing varies |
| Cloudflare R2 | 10GB storage | $0.015/GB/month |
| Cloudflare R2 | Unlimited egress | $0 (zero!) |
| Cloudflare Pages | Unlimited sites | $0 |
| Total (small scale) | **$0/month** | - |

---

## 7. Troubleshooting

### Leonardo AI Issues

**"Rate limited"**
- Wait for daily credit reset, or upgrade API plan

**"Generation failed"**
- Check prompt for prohibited content
- Try different model (Phoenix vs Lightning)

### R2 Issues

**"Access denied"**
- Verify API token has R2 permissions
- Check bucket is set to public access

**"CORS error"**
- Add CORS rules in R2 bucket settings

### Image Quality Issues

**"Images look pixelated"**
- Increase generation resolution in Leonardo
- Check WebP quality setting (use 80+)

**"Hero doesn't cover full width"**
- Ensure `background-size: cover` in CSS
- Check image aspect ratio matches container

---

## References

- [Leonardo AI MCP Server](https://github.com/ish-joshi/leonardo-mcp-server)
- [Cloudflare MCP Servers](https://developers.cloudflare.com/agents/model-context-protocol/mcp-servers-for-cloudflare/)
- [Cloudflare R2 Documentation](https://developers.cloudflare.com/r2/)
- [Claude Code MCP Setup](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [WebP Format Guide](https://developers.google.com/speed/webp)
