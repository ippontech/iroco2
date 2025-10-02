# IroCO2 🌱

**IroCO2** is a tool designed to help organizations estimate and reduce the carbon footprint of their cloud infrastructures. Developed by Ippon Technologies, IroCO2 enables organizations to measure their environmental impact and supports them in their ecological transition. The tool combines detailed technical data (when available) with extrapolations from reference studies to address data gaps. IroCO2 provides both a predictive estimate of infrastructure carbon emissions and ongoing tracking through an infrastructure scanner. Currently compatible with AWS services, IroCO2 will soon support other cloud providers such as Microsoft Azure, Google Cloud, OVH, and on-premise solutions.

This project is the main repository of the IroCO2 project. It contains general documentation and the installation instructions.

🏗️ **Important** 🏗️ This project is currently in development and is going open source as soon as possible. Some parts of documentation or code may not be up to date or missing for the moment. If you think details are missing or if you have questions, please open an issue.

## Context

The project development started in 2023 and was first internal to Ippon Technologies. It has been developed aiming to become a SaaS solution provided by Ippon. Now we have made the decision to open source the project, allowing external contributions and the community to benefit from the project.

Today, we want to allow self-hosting of the solution. The Terraform currently present in the repositories is the one we used to maintain the SaaS deployment. We plan to improve it in our next steps of development (ex. A simpler and unique terraform module to deploy the solution).

This implies a lot of critical changes in the codebase and the architecture. If you want to self-host the solution, you can use the Terraform currently present in the repositories by reading this [documentation](./deployment/guide.md). Please, create any issue if you have problems regarding the self-hosting process, this will allow us to create a better documentation and improve the Terraform modules.

## What IroCO2 propose ?

IroCO2 proposes a solution to measure and reduce the carbon footprint of your cloud infrastructures. It can be used to measure the carbon footprint of your AWS infrastructure and provide recommendations to reduce it.

The solution is made up from 3 main tools:

- **The Carbon footprint Calculator/Designer** : Inspired by the AWS Pricing Calculator, it allows you to estimate the carbon footprint of your cloud infrastructure before deploying it, allowing you to include sustainability by design.
- **The CUR Analysis** : Import a CUR file to analyze the carbon footprint of your cloud infrastructure once.
- **The Carbon footprint Scanner** : Scan your cloud infrastructure to analyze the carbon footprint of your cloud infrastructure in real-time.

## 📦 Project Structure

The project repositories are structured as follows:

- [iroco2-frontend](https://github.com/ippontech/iroco2-frontend): The frontend repository.
- [iroco2-backend](https://github.com/ippontech/iroco2-backend): The backend repository.
- [iroco2-lambdas](https://github.com/ippontech/iroco2-lambdas): The lambda functions repository.
- [iroco2-terraform-modules](https://github.com/ippontech/iroco2-terraform-modules): The terraform modules repository.

## 📄 Documentation

See the general documentation website [here](https://ippontech.github.io/iroco2/#/).

See [docs/](./docs) for architecture and technical decisions.

## Support

If you have any questions or need support, please check if this problem has already been reported in the [issue tracker](https://github.com/ippontech/iroco2/issues). If not, please [create an issue](https://github.com/ippontech/iroco2/issues/new/choose).

## 🤝 Contributing
See [CONTRIBUTING.md](./contribute/CONTRIBUTING.md)
