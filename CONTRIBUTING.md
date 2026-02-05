# Contributing to MedTrust

First off, thank you for considering contributing to MedTrust! It's people like you that make MedTrust such a great tool for improving healthcare accessibility in India.

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When you create a bug report, include as many details as possible:

- **Use a clear and descriptive title**
- **Describe the exact steps to reproduce the problem**
- **Provide specific examples**
- **Describe the behavior you observed and what you expected**
- **Include screenshots if applicable**
- **Include your environment details** (OS, device, app version)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- **Use a clear and descriptive title**
- **Provide a detailed description of the suggested enhancement**
- **Explain why this enhancement would be useful**
- **List any alternative solutions you've considered**

### Pull Requests

1. Fork the repo and create your branch from `main`
2. If you've added code that should be tested, add tests
3. If you've changed APIs, update the documentation
4. Ensure the test suite passes
5. Make sure your code lints
6. Issue that pull request!

## Development Process

### Setting Up Your Development Environment

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/MedTrust.git
cd MedTrust

# Add upstream remote
git remote add upstream https://github.com/mjpvl-ai/MedTrust.git

# Install dependencies
npm install

# Create a branch for your feature
git checkout -b feature/your-feature-name
```

### Coding Standards

- **TypeScript**: Use TypeScript for all new code
- **ESLint**: Follow the ESLint configuration
- **Prettier**: Format code with Prettier
- **Comments**: Write clear comments for complex logic
- **Tests**: Write tests for new features

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
feat: add voice output for Tamil language
fix: resolve OCR accuracy issue for handwritten prescriptions
docs: update API documentation
test: add property tests for medicine database
refactor: improve prescription parsing logic
```

### Testing

```bash
# Run all tests
npm test

# Run specific test suite
npm test -- prescription.test.ts

# Run with coverage
npm run test:coverage

# Run property-based tests
npm run test:property
```

### Code Review Process

1. All submissions require review
2. We use GitHub pull requests for this purpose
3. Reviewers will check for:
   - Code quality and style
   - Test coverage
   - Documentation
   - Performance implications
   - Security considerations

## Project Structure

```
MedTrust/
├── src/
│   ├── components/     # React Native components
│   ├── screens/        # App screens
│   ├── services/       # Business logic
│   ├── utils/          # Utility functions
│   ├── types/          # TypeScript types
│   └── constants/      # Constants
├── docs/               # Documentation
├── tests/              # Test files
├── .kiro/specs/        # Specifications
└── aws/                # AWS infrastructure code
```

## Areas We Need Help

- 🌐 **Translations**: Help translate to more Indian languages
- 🧪 **Testing**: Write more tests, especially property-based tests
- 📱 **UI/UX**: Improve user interface and experience
- 🔬 **Medical Accuracy**: Review medical content for accuracy
- 📚 **Documentation**: Improve documentation and examples
- 🐛 **Bug Fixes**: Fix reported bugs
- ✨ **Features**: Implement new features from the roadmap

## Recognition

Contributors will be:
- Listed in our README
- Mentioned in release notes
- Invited to our contributor community
- Eligible for contributor swag (coming soon!)

## Questions?

Feel free to:
- Open an issue with the `question` label
- Email us at dev@medtrust.health
- Join our Discord community (link coming soon)

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for contributing to MedTrust! Together, we're making healthcare accessible to every Indian. 🙏
