# Shiprocket → MongoDB Python Workflow

This project converts the supplied n8n workflow into Python for VS Code.

## Workflow

1. Runs immediately and then on a configurable interval (default: 5 seconds).
2. Fetches Shiprocket orders for today and the previous `DAYS_BACK` days.
3. Handles pagination and retries failed HTTP requests.
4. Creates one clean MongoDB document per product.
5. Upserts using `recordKey = orderId:productId` so multi-product orders are not overwritten.
6. Sends an SMTP/Gmail error alert when an execution fails.

## VS Code setup

Open this folder in VS Code, then run these commands in the integrated terminal:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
source .venv/bin/activate
```

Install packages:

```bash
python -m pip install -r requirements.txt
```

Copy `.env.example` to `.env`, then add a **fresh** Shiprocket token and your MongoDB details.

Run continuously:

```bash
python main.py
```

Run only once by setting this in `.env`:

```env
RUN_ONCE=true
```

You can also press **F5** in VS Code and choose **Run Shiprocket workflow**.

## MongoDB

Default target:

- Database: `hoora`
- Collection: `shiprocket_orders`

Indexes are created automatically on `recordKey`, `orderId`, and `orderDate`.

## Gmail alerts

Use a Google App Password rather than your normal Gmail password. Set the SMTP variables in `.env`. If SMTP values are empty, failures are still logged but no email is sent.

## Security

The original workflow contained a bearer token in plain text. Revoke/rotate that token and never commit `.env` to Git.
