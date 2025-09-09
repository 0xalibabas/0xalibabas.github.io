# 0x_alibabas - Red Team Security Blog

A cybersecurity blog focused on Red Team operations, penetration testing, and offensive security research.

## 🎯 About

This blog covers:

- **Red Team Operations**: Real-world attack simulations and methodologies
- **Penetration Testing**: Techniques, tools, and case studies  
- **Vulnerability Research**: CVE analysis and exploit development
- **Security Tools**: Reviews and custom tool development
- **Threat Intelligence**: Analysis of current threats and attack vectors

## 🚀 Live Site

Visit the blog at: [https://0xalibabas.github.io](https://0xalibabas.github.io)

## 🛠️ Built With

- [Hugo](https://gohugo.io/) - Static site generator
- [PaperMod Theme](https://github.com/adityatelange/hugo-PaperMod) - Clean and responsive theme
- [GitHub Pages](https://pages.github.com/) - Hosting and deployment

## 📝 Local Development

1. Install Hugo:
   ```bash
   # Windows (using Chocolatey)
   choco install hugo -y
   
   # Or download from https://github.com/gohugoio/hugo/releases
   ```

2. Clone the repository:
   ```bash
   git clone https://github.com/0xalibabas/0xalibabas.github.io.git
   cd 0xalibabas.github.io
   ```

3. Install theme submodule:
   ```bash
   git submodule update --init --recursive
   ```

4. Run the development server:
   ```bash
   hugo server --buildDrafts
   ```

5. Open [http://localhost:1313](http://localhost:1313) in your browser

## 📖 Writing Posts

Create a new post:
```bash
hugo new content posts/your-post-title.md
```

## 🔧 Configuration

The main configuration is in `hugo.toml`. Key settings:

- `baseURL`: Your GitHub Pages URL
- `theme`: PaperMod theme
- `params`: Blog metadata and social links

## 📄 License

This blog content is licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

## 🤝 Contributing

Feel free to open issues or pull requests for:

- Content suggestions
- Bug fixes
- Feature requests
- Security improvements

## 📞 Contact

- GitHub: [@0xalibabas](https://github.com/0xalibabas)
- Blog: [0xalibabas.github.io](https://0xalibabas.github.io)

---

*"To defend effectively, you must think like an attacker."*
