# Installation Scripts for Cybersecurity Home Lab

This directory contains various installation scripts necessary for setting up the components of the cybersecurity home lab. Follow the instructions below to utilize these scripts effectively.

## Prerequisites

Before running the installation scripts, ensure that you have the following prerequisites installed on your system:

- Python 3.x
- Git
- Virtual Environment (optional but recommended)

## Installation Instructions

1. **Clone the Repository**
   ```bash
   git clone https://github.com/yourusername/cybersecurity-home-lab.git
   cd cybersecurity-home-lab/src/setup/install_scripts
   ```

2. **Set Up a Virtual Environment (Optional)**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Run the Installation Scripts**
   - Make sure the scripts are executable:
     ```bash
     chmod +x *.sh
     ```
   - Execute the desired installation script:
     ```bash
     ./install_script_name.sh
     ```

## Available Scripts

- `install_script_name.sh`: Description of what this script does.
- Add more scripts as necessary with their descriptions.

## Troubleshooting

If you encounter any issues while running the scripts, please check the following:

- Ensure all prerequisites are installed.
- Review the script output for any error messages.
- Consult the documentation in the `configs` and `docs` directories for additional guidance.

## Contribution

Feel free to contribute to this project by submitting issues or pull requests. Your feedback and improvements are welcome!