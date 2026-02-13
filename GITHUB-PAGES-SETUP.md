# Setting Up GitHub Pages for Documentation

This guide will help you deploy your documentation to GitHub Pages automatically using GitHub Actions.

## 🚀 Quick Setup (5 minutes)

### Step 1: Enable GitHub Pages

1. **Go to your repository on GitHub:**
   `https://github.com/ahmad2point0/howicode`

2. **Navigate to Settings:**
   - Click the "Settings" tab in your repository

3. **Configure Pages:**
   - Scroll down to "Pages" in the left sidebar
   - Under "Source", select **"GitHub Actions"**
   - ✅ This will use our automated workflow

### Step 2: Commit and Push Your Changes

```bash
# Add all the new files
git add .

# Commit the changes
git commit -m "feat: add GitHub Pages documentation setup"

# Push to GitHub
git push origin main
```

### Step 3: Watch the Magic Happen

1. **Go to the Actions tab** in your GitHub repository
2. **Watch the workflow run** - it takes about 2-3 minutes
3. **Check the deployment** - your site will be live at:
   
   **🌐 https://ahmad2point0.github.io/howicode/**

## 🔧 What Happens Automatically

Our GitHub Actions workflow (`.github/workflows/pages.yml`) will:

1. **Trigger on every push** to the `main` branch
2. **Install Ruby and Jekyll** in the GitHub runner
3. **Install all gems** from your `Gemfile`
4. **Build the documentation** using Jekyll
5. **Deploy to GitHub Pages** automatically

## 📁 Files Created for GitHub Pages

```
.github/
└── workflows/
    └── pages.yml          # GitHub Actions workflow

_config.yml               # Jekyll configuration
index.md                  # Homepage
contributing.md           # Contributor guide
react-native-expo.md      # Template documentation
Gemfile                   # Ruby dependencies
```

## 🛠️ Configuration Details

### GitHub Actions Workflow Features

- ✅ **Automatic deployment** on push to main
- ✅ **Manual trigger** option from Actions tab
- ✅ **Ruby 3.2** with automatic gem caching
- ✅ **Jekyll production build** with proper baseurl
- ✅ **Secure deployment** with proper permissions

### Jekyll Configuration Highlights

```yaml
# _config.yml key settings
title: React App Folder Structure Templates
baseurl: "/howicode"
url: "https://ahmad2point0.github.io"
theme: just-the-docs
```

## 🔍 Verification Steps

After deployment, verify everything works:

1. **Visit your site:** https://ahmad2point0.github.io/howicode/
2. **Test navigation:** Click through different pages
3. **Test search:** Use the search functionality
4. **Check mobile:** View on mobile devices
5. **Verify links:** Make sure all internal links work

## 🐛 Troubleshooting

### Common Issues:

**1. Workflow fails to build:**
- Check the Actions tab for error details
- Most common: Gemfile issues or Jekyll configuration errors

**2. Site loads but styling is broken:**
- Check the `baseurl` in `_config.yml`
- Make sure it matches your repository name

**3. Links don't work:**
- Use relative links for internal pages
- Example: `[Contributing](contributing.html)`
- For markdown files, use `.html` extension in links

**4. Images don't load:**
- Place images in `assets/images/`
- Use relative paths: `![Image](./assets/images/screenshot.png)`

### Debug Commands (Local Testing):

```bash
# Test the exact build process GitHub uses
JEKYLL_ENV=production bundle exec jekyll build --baseurl "/howicode"

# Serve with the same baseurl
bundle exec jekyll serve --baseurl "/howicode"
```

## 🔄 Workflow Triggers

The documentation will automatically rebuild when you:

- ✅ **Push to main branch** - Automatic deployment
- ✅ **Manual trigger** - From GitHub Actions tab
- ✅ **Pull request merge** - When PRs are merged to main

## 📊 Monitoring

### Check Deployment Status:

1. **Actions Tab:** See build and deployment progress
2. **Environments:** View deployment history and URLs
3. **Pages Settings:** Monitor overall Pages configuration

### Performance:

- **Build time:** ~2-3 minutes for full deployment
- **Cache:** Ruby gems are cached for faster subsequent builds
- **CDN:** GitHub Pages uses a global CDN for fast loading

## 🎯 Custom Domain (Optional)

To use a custom domain like `docs.yoursite.com`:

1. **Add CNAME file:**
   ```bash
   echo "docs.yoursite.com" > CNAME
   git add CNAME && git commit -m "add custom domain"
   ```

2. **Configure DNS:**
   - Add CNAME record pointing to `ahmad2point0.github.io`

3. **Update _config.yml:**
   ```yaml
   url: "https://docs.yoursite.com"
   baseurl: ""
   ```

## 🚀 Next Steps

After your documentation is live:

1. **Share the link** with contributors
2. **Add the URL** to your repository description
3. **Include it in your main project README**
4. **Set up branch protection** to require PR reviews

## 📝 Maintenance

Your documentation will stay automatically updated as you:

- Add new framework templates
- Update existing documentation
- Accept contributor pull requests

The GitHub Actions workflow handles everything automatically! 🎉

---

**🌐 Your documentation will be live at:**
**https://ahmad2point0.github.io/howicode/**
