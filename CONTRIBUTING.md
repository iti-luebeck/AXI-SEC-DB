# Contributing to AXI-SEC-DB

Thank you for your interest in contributing to the AXI Protocol Security Dataset! This document provides guidelines for contributing to the repository.

## 🎯 Ways to Contribute

We welcome contributions in several forms:

### 1. **Dataset Enhancements**
- Additional attack scenarios or variations
- Data from different SoC platforms or FPGA boards
- Extended normal traffic patterns
- Temporal sequences for time-series analysis

### 2. **Code Contributions**
- Preprocessing scripts and utilities
- Visualization tools
- ML model implementations
- Data loading and parsing utilities

### 3. **Documentation**
- Usage examples and tutorials
- Jupyter notebooks demonstrating analysis
- Additional attack scenario descriptions
- Platform-specific setup guides

### 4. **Bug Fixes**
- Correct data formatting issues
- Fix script errors
- Improve documentation clarity

## 📋 Contribution Guidelines

### Before You Start

1. **Check existing issues** - Look for related issues or discussions
2. **Open an issue** - Describe your proposed contribution
3. **Wait for feedback** - Ensure alignment with project goals
4. **Fork the repository** - Create your development branch

### Code Standards

#### Python Code
- Follow PEP 8 style guidelines
- Include docstrings for all functions
- Add type hints where applicable
- Write unit tests for new functionality

Example:
```python
def load_axi_data(file_path: str, format: str = 'csv') -> pd.DataFrame:
    """
    Load AXI transaction data from file.
    
    Args:
        file_path: Path to the data file
        format: File format ('csv', 'npy', 'vcd')
    
    Returns:
        DataFrame containing AXI transactions
    
    Raises:
        FileNotFoundError: If file doesn't exist
        ValueError: If format is unsupported
    """
    # Implementation
```

#### Documentation
- Use clear, concise language
- Provide code examples where relevant
- Include references to paper sections when applicable
- Use Markdown formatting consistently

### Commit Messages

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code formatting (no functional changes)
- `refactor:` Code restructuring
- `test:` Adding or modifying tests
- `chore:` Maintenance tasks

Examples:
```
feat: add LSTM model training script
fix: correct AWLEN parsing in preprocessing pipeline
docs: update attack scenario descriptions with waveforms
```

### Pull Request Process

1. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**
   - Write clear, well-documented code
   - Add tests for new functionality
   - Update relevant documentation

3. **Test your changes**
   ```bash
   # Run tests
   pytest tests/
   
   # Check code style
   flake8 scripts/
   ```

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "feat: add your feature description"
   ```

5. **Push to your fork**
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Open a Pull Request**
   - Provide a clear title and description
   - Reference related issues
   - Explain the motivation and impact
   - Include screenshots/outputs if applicable

### Pull Request Template

```markdown
## Description
Brief description of changes

## Motivation
Why is this change needed?

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement

## Testing
How was this tested?

## Checklist
- [ ] Code follows project style guidelines
- [ ] Documentation updated
- [ ] Tests added/updated
- [ ] All tests pass
- [ ] Commit messages follow conventions
```

## 🔬 Dataset Contribution Guidelines

### Adding New Data

If contributing additional AXI transaction data:

1. **Data Collection**
   - Document platform (SoC, FPGA board, tools)
   - Specify operating conditions
   - Describe attack methodology (if malicious)
   - Include timestamps and metadata

2. **Data Format**
   - Provide data in multiple formats (CSV, NPY, VCD)
   - Follow existing schema and naming conventions
   - Include header documentation
   - Ensure consistent signal naming

3. **Validation**
   - Verify data integrity
   - Confirm protocol compliance (for normal traffic)
   - Validate attack effectiveness (for malicious traffic)
   - Check for duplicate samples

4. **Documentation**
   - Update README with new data statistics
   - Add attack scenario descriptions if applicable
   - Include collection methodology in docs/
   - Provide usage examples

### Data Quality Standards

All contributed data must:
- Be original or properly attributed
- Follow AXI4 protocol specifications
- Include clear labels (normal/malicious)
- Contain no corrupted or invalid entries
- Be reproducible (methodology documented)

## 🧪 Testing

### Running Tests

```bash
# Install test dependencies
pip install -r requirements-dev.txt

# Run all tests
pytest tests/

# Run specific test file
pytest tests/test_data_loading.py

# Run with coverage
pytest --cov=scripts tests/
```

### Writing Tests

All new functions should include unit tests:

```python
# tests/test_preprocessing.py
import pytest
from scripts.preprocessing_pipeline import encode_signals

def test_encode_signals():
    """Test signal encoding from binary to decimal."""
    raw_data = {'AWLEN': '0b1111', 'AWID': '0x0A'}
    encoded = encode_signals(raw_data)
    
    assert encoded['AWLEN'] == 15
    assert encoded['AWID'] == 10
```

## 📧 Communication

### Reporting Issues

When reporting bugs or issues:

1. **Check existing issues** first
2. **Use issue templates** if available
3. **Provide detailed information**:
   - Clear description of the problem
   - Steps to reproduce
   - Expected vs actual behavior
   - System information (OS, Python version)
   - Error messages and stack traces

### Feature Requests

For feature requests:

1. **Explain the use case** - Why is this needed?
2. **Describe the solution** - How should it work?
3. **Consider alternatives** - What other approaches exist?
4. **Assess impact** - Who benefits from this?

### Questions and Discussions

- Use GitHub Discussions for general questions
- Use Issues for bug reports and feature requests
- Email authors for collaboration inquiries

## 🏆 Recognition

Contributors will be:
- Listed in CONTRIBUTORS.md
- Acknowledged in release notes
- Credited in documentation where applicable

Significant contributions may warrant co-authorship on future publications using the extended dataset.

## 📜 Code of Conduct

### Our Standards

- **Be respectful** - Treat all contributors with respect
- **Be collaborative** - Work together constructively
- **Be inclusive** - Welcome diverse perspectives
- **Be professional** - Maintain academic and professional standards

### Unacceptable Behavior

- Harassment or discrimination
- Trolling or inflammatory comments
- Publishing others' private information
- Unethical use of the dataset

### Enforcement

Violations may result in:
1. Warning from maintainers
2. Temporary ban from repository
3. Permanent ban for severe/repeated violations

Report violations to: saleh.mulhem@uni-luebeck.de

## 📄 License

By contributing to AXI-SEC-DB, you agree that your contributions will be licensed under the [CC BY 4.0](LICENSE) license.

For code contributions, you retain copyright but grant the project maintainers permission to use, modify, and distribute your code.

## ✅ Checklist for Contributors

Before submitting a contribution:

- [ ] I have read the contributing guidelines
- [ ] My code follows the project style
- [ ] I have updated documentation
- [ ] I have added tests for new functionality
- [ ] All tests pass locally
- [ ] I have updated CHANGELOG.md (if applicable)
- [ ] My commit messages follow conventions
- [ ] I have referenced related issues
- [ ] I agree to the license terms

## 🙏 Thank You!

Your contributions help advance hardware security research and make this dataset more valuable to the community. We appreciate your time and effort!

---

**Questions?** Contact the maintainers:
- Wadid Foudhaili: wadid.foudhaili@uni-luebeck.de

**Found this helpful?** Star the repository and spread the word!
