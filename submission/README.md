# FlyRank Content Refresh Prioritization

## 1. What It Does

This project helps content teams prioritize web pages that are more likely to decline in search performance and may need a content refresh.

The system uses historical page-level signals and a machine learning model to rank pages by their likelihood of decline. The resulting priority queue helps reviewers focus their limited time on pages with stronger model signals instead of reviewing every page manually.

The model is designed as a decision-support tool. A high-ranked page is a candidate for review and refresh; the prediction does not guarantee that refreshing the page will improve its future performance.


## 2. Who It Is For

This project is designed for content teams, SEO teams, and website reviewers who need to identify and prioritize pages that may require a content refresh.

Instead of manually reviewing every page, the system provides a ranked priority queue that helps reviewers focus their attention on pages with stronger signals of potential performance decline.

The final decision to refresh a page remains with the human reviewer.


## 3. Setup

### Requirements

Before running the project, make sure the following are installed:

- Python 3.x
- Git
- pip
- Required Python dependencies listed in `requirements.txt`

### Clone the Repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_PROJECT_FOLDER>
