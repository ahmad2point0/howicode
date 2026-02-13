# Installing Ruby and Gems on Windows

This guide will help you install Ruby, Bundler, and the required gems to run the documentation locally.

## 🪟 Windows Installation

### Option 1: RubyInstaller (Recommended)

1. **Download RubyInstaller:**
   - Visit [https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)
   - Download the latest **Ruby+Devkit** version (e.g., Ruby+Devkit 3.2.X)
   - Choose the x64 version for 64-bit Windows

2. **Install Ruby:**
   - Run the downloaded installer
   - Check "Add Ruby executables to your PATH"
   - Check "Associate .rb and .rbw files with this Ruby installation"
   - Install with default settings

3. **Install MSYS2 Development Toolchain:**
   - After Ruby installation, a command prompt will open
   - When prompted, press `Enter` to install MSYS2 and development toolchain
   - This is required for native gem compilation

### Option 2: Using Chocolatey

If you have Chocolatey package manager:

```cmd
choco install ruby
```

### Option 3: Using winget

If you have Windows Package Manager:

```cmd
winget install RubyInstallerTeam.Ruby
```

## 🔧 Verify Installation

Open a new Command Prompt or PowerShell and run:

```cmd
ruby --version
gem --version
```

You should see version numbers for both commands.

## 📦 Install Bundler

Install the Bundler gem globally:

```cmd
gem install bundler
```

## 🚀 Install Project Dependencies

1. **Navigate to your project directory:**
   ```cmd
   cd path/folder-structure
   ```

2. **Install all gems from Gemfile:**
   ```cmd
   bundle install
   ```

   This will install:
   - `github-pages` - For GitHub Pages compatibility
   - `just-the-docs` - The documentation theme
   - `wdm` - Windows directory monitoring (for auto-reload)
   - Other dependencies

## 🏃‍♂️ Run the Documentation Site

After installation, start the Jekyll server:

```cmd
bundle exec jekyll serve
```

The site will be available at: `http://localhost:4000`

## 🔧 Troubleshooting

### Common Issues:

**1. SSL Certificate Errors:**
```cmd
gem update --system
```

**2. Permission Errors:**
Run Command Prompt as Administrator and retry.

**3. Native Extension Build Errors:**
Make sure you installed Ruby+Devkit and the MSYS2 toolchain.

**4. Bundle Install Fails:**
Try updating RubyGems:
```cmd
gem update --system
gem cleanup
bundle install
```

**5. Jekyll Command Not Found:**
```cmd
gem install jekyll
# Then try again
bundle exec jekyll serve
```

### Alternative: Using Docker

If you encounter persistent issues, you can use Docker:

```cmd
# Build and run with Docker
docker run --rm -v "%cd%":/usr/src/app -p 4000:4000 jekyll/jekyll:latest jekyll serve --host 0.0.0.0
```

## 📝 Development Workflow

1. **Make changes** to documentation files
2. **Jekyll auto-reloads** (thanks to `wdm` gem on Windows)
3. **View changes** at `http://localhost:4000`
4. **Commit and push** when satisfied

## 🆘 Need Help?

- [Ruby Installation Guide](https://www.ruby-lang.org/en/documentation/installation/)
- [Jekyll on Windows](https://jekyllrb.com/docs/installation/windows/)
- [Just the Docs Documentation](https://just-the-docs.github.io/just-the-docs/)

## 🎯 Quick Commands Reference

```cmd
# Check versions
ruby --version
gem --version
bundler --version

# Install dependencies
bundle install

# Update gems
bundle update

# Start development server
bundle exec jekyll serve

# Build static site
bundle exec jekyll build

# Clean build files
bundle exec jekyll clean
```
