# Gus Feliciano's Personal Website

Welcome to the repository for my personal website! This site serves as a hub for my professional information, projects, and potentially blog posts. You can visit the live site at [https://gusfeliciano.github.io](https://gusfeliciano.github.io).

## About This Site

This website is built using [Hugo](https://gohugo.io/), a fast and flexible static site generator. It uses a modified version of the [charlolamode theme](https://github.com/charlola/hugo-theme-charlolamode), which has been customized to fit my personal style and needs.

## Features

- Responsive design
- Dark/Light mode
- Profile section with customizable image and buttons
- Social icons
- Optional blog section (currently commented out)
- Resume download option

## How It Works

This site is built with Hugo and deployed to GitHub Pages using GitHub Actions. The workflow is defined in the `.github/workflows/hugo.yaml` file. When changes are pushed to the `main` branch, the workflow automatically builds the site and deploys it to GitHub Pages.

## How to Create a Similar Website

If you'd like to create a similar website for yourself, here's a step-by-step guide:

1. **Prerequisites**
   - Install [Hugo](https://gohugo.io/installation/) on your local machine
   - Have a [GitHub](https://github.com/) account

2. **Setup**
   - Fork this repository or create a new repository named `yourusername.github.io`
   - Clone the repository to your local machine

3. **Customize**
   - Update the `config.yml` file with your personal information
   - Replace the profile image in the `static/images` directory
   - Modify or add content in the `content` directory
   - Update the `static/Gustavo.Feliciano.Resume.pdf` with your own resume (or update the link in `config.yml`)

4. **Local Development**
   - Run `hugo server -D` in the root directory of your project
   - Visit `http://localhost:1313` to see your site locally

5. **Deploy**
   - Push your changes to the `main` branch of your GitHub repository
   - GitHub Actions will automatically build and deploy your site
   - Your site will be live at `https://yourusername.github.io`

## Customization Notes

- This setup uses a copy of the theme files rather than a Git submodule for better long-term stability
- You can uncomment sections in `config.yml` to enable additional features like the blog or projects sections

## Contributing

Feel free to submit issues or pull requests if you have suggestions for improvements or find any bugs.

## License

This project is open source and available under the [MIT License](LICENSE).

---

Thank you for visiting my personal website repository. I hope you find it inspiring and informative. Feel free to reach out if you have any questions or suggestions!