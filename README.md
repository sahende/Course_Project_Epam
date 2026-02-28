# InnovatEPAM Portal - AITech Bootcamp Capstone Project

## Project Purpose
InnovatEPAM Portal is a capstone project for the EPAM AITech Bootcamp. It is a web application designed to help employees within an organization easily submit, share, and manage ideas. Its goal is to centralize innovative ideas and make them quickly reviewable and actionable.

## Key Features
- **User Management**: Registration, login, logout, and role-based access (employee vs. admin/evaluator)  
- **Idea Submission**: Create ideas with title, description, and category  
- **File Attachment**: Upload a single supporting file per idea  
- **Idea Listing**: View all submitted ideas and track their status  
- **Evaluation Workflow**: Monitor idea status and allow admins to accept/reject with comments  
- **Advanced Forms & Drafts**: Dynamic form fields and ability to save drafts (Phase 2 feature)  

## User Flow
1. Employees register and log in to the platform  
2. Submit new ideas and attach files if needed  
3. Track the status of submitted ideas  
4. Admins review ideas, provide feedback, and update their status  

## Technology Stack
- **Frontend**: Next.js (React)  
- **Backend / Database**: PostgreSQL via Prisma ORM  
- **Authentication**: JWT with server-side refresh tokens  

## Test Results
- **Total Tests**: 66 tests across 31 test suites  
- **Passing Tests**: 100% locally  
- **Overall Test Coverage**: 94.43%  

## Outcome
The platform enables efficient idea management, simplifies evaluation workflows, and encourages innovation within the organization. Automated tests ensure reliability and reduce bugs, making the system robust and maintainable.

<img width="945" height="442" alt="image" src="https://github.com/user-attachments/assets/a72321b2-15f9-4c4e-9d59-ca670ee16416" />
<img width="945" height="480" alt="image" src="https://github.com/user-attachments/assets/14de0839-d1f9-4f01-9b6a-2016f721f460" />
<img width="945" height="526" alt="image" src="https://github.com/user-attachments/assets/645d71b0-dffd-4034-90b3-5bfe22d51c16" />
<img width="945" height="307" alt="image" src="https://github.com/user-attachments/assets/d2704aeb-726e-4de9-9a13-340c074baaea" />
<img width="945" height="499" alt="image" src="https://github.com/user-attachments/assets/15906077-9497-4259-92c7-35766174e78c" />
<img width="945" height="570" alt="image" src="https://github.com/user-attachments/assets/2d484b58-cb0a-42f7-87ba-80c897e5cf63" />







## Admin / Evaluator bootstrap (emails & domains)

This project supports bootstrapping admin/evaluator accounts via environment config. Use this only for initial bootstrap — production role management should use your IdP or a DB-driven admin UI.

There are two mechanisms:

- `ADMIN_EMAILS`: exact email list (e.g. `admin2@example.com,admin3@example.com`)
- `ADMIN_EMAIL_DOMAINS`: domain list (e.g. `example.com`) — **any email ending with this domain is treated as evaluator/admin** on first registration.

Local example (.env):

```
# .env example for local development
DATABASE_URL="postgresql://portal_user:your_password@localhost:5432/portal_db"

# Admin/evaluator emails (comma-separated, lowercased)
# Exact addresses promoted to evaluator/admin
ADMIN_EMAILS="admin@example.com"

# Or promote all users from a domain to evaluator/admin on registration
ADMIN_EMAIL_DOMAINS="example.com"
```

Deployment / CI guidance (GitHub Actions):

1. Add a repository secret named `ADMIN_EMAILS` with the comma-separated admin emails (e.g. `alice@example.com,bob@example.com`).
2. Ensure your deployment pipeline injects that secret into the runtime environment (do not commit `.env` with secrets).

Notes:
- On startup the application will read `ADMIN_EMAILS` to seed admin roles if a corresponding user exists or to mark seeded accounts; this is intended as a bootstrap only.
- Long-term: integrate role/group claims from your Identity Provider (IdP) or maintain roles in the database and provide an admin UI for role management.
- Security: keep `ADMIN_EMAILS` in your secrets manager (GitHub Secrets, Azure Key Vault, AWS Secrets Manager, etc.) and rotate access appropriately.

### GitHub Actions example (run after migrations)

Example workflow step to run the seed script during deployment. This uses the repo secret `ADMIN_EMAILS` and does not print the secret value.

```yaml
- name: Seed admin users
	run: |
		npm ci
		npm run build
		# Run seed script (ADMIN_EMAILS provided via secrets)
		ADMIN_EMAILS="${{ secrets.ADMIN_EMAILS }}" node scripts/seed-admins.js
	# Do not echo or log the secret value anywhere; GitHub masks secrets automatically.
	shell: bash
```

Security scanner note:
- Do not commit `.env` or plaintext secrets. GitHub Actions masks secrets, so avoid printing them. If you have an automated secret-scanner in CI that blocks deployments for certain secret names, configure it to accept the `ADMIN_EMAILS` secret usage for this bootstrap flow or prefer a secrets manager integration (recommended).
