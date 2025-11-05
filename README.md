# CashFlow - Personal Finance Tracker

A simple, elegant personal finance tracker that helps you manage your income, expenses, and cash flow with ease. Track your transactions, monitor cash in hand, and reconcile your finances—all in a clean, intuitive interface.

## Features

### Core Functionality
- **Transaction Management**: Track all your income and expenses with running balance calculations
- **Cash Reconciliation**: Compare your calculated balance with actual cash in hand to identify untracked expenses
- **Upcoming Transactions**: Plan ahead by tracking upcoming receivables and outgoings
- **Cash History**: Historical tracking of your cash in hand over time
- **User Authentication**: Secure login and signup powered by Supabase

### Transaction Features
- Add income and expense entries with dates and descriptions
- Edit and delete existing transactions
- Reorder transactions that occur on the same day
- Mark upcoming transactions as completed
- View running balance after each transaction

### Cash Management
- Track actual cash in hand
- Automatic calculation of untracked cash expenses
- Historical cash snapshots with timestamps
- Visual reconciliation display

### User Interface
- Clean, modern design with responsive layout
- Color-coded statistics (income, expenses, balance, cash)
- Highlighted upcoming transactions for easy identification
- Mobile-friendly interface

## Technology Stack

- **Frontend**: Vanilla JavaScript, HTML5, CSS3
- **Backend**: Supabase (PostgreSQL database, Authentication)
- **Styling**: Custom CSS with modern design patterns
- **Hosting**: Static HTML file (can be hosted anywhere)

## Getting Started

### Prerequisites
- A Supabase account and project
- A web browser
- A web server (or simply open the HTML file locally)

### Database Setup

You'll need to create the following tables in your Supabase project:

#### 1. transactions
```sql
CREATE TABLE transactions (
    id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    description TEXT NOT NULL,
    debit DECIMAL(10, 2) DEFAULT 0,
    credit DECIMAL(10, 2) DEFAULT 0,
    type TEXT DEFAULT 'completed',
    display_order INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_transactions_user_date ON transactions(user_id, date);
CREATE INDEX idx_transactions_user_order ON transactions(user_id, display_order);
```

#### 2. upcoming_expenses
```sql
CREATE TABLE upcoming_expenses (
    id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
    description TEXT NOT NULL,
    debit DECIMAL(10, 2) DEFAULT 0,
    credit DECIMAL(10, 2) DEFAULT 0,
    display_order INTEGER DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_upcoming_user_order ON upcoming_expenses(user_id, display_order);
```

#### 3. cash_history
```sql
CREATE TABLE cash_history (
    id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
    date TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    amount DECIMAL(10, 2) NOT NULL,
    calculated_balance DECIMAL(10, 2),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

CREATE INDEX idx_cash_history_user_date ON cash_history(user_id, date DESC);
```

#### 4. user_settings
```sql
CREATE TABLE user_settings (
    user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    actual_cash DECIMAL(10, 2) DEFAULT 0,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### Configuration

1. Open `index.html` in a text editor
2. Find the Supabase configuration section (around line 656-657):
   ```javascript
   const SUPABASE_URL = 'your-project-url.supabase.co';
   const SUPABASE_ANON_KEY = 'your-anon-key';
   ```
3. Replace with your Supabase project URL and anon key
4. Save the file

### Running the Application

1. **Local Development**:
   - Simply open `index.html` in your web browser
   - Or use a local server: `python -m http.server 8000`

2. **Deployment**:
   - Upload `index.html` to any static hosting service
   - Examples: GitHub Pages, Netlify, Vercel, AWS S3, etc.

## Usage

### Getting Started
1. **Sign Up**: Create a new account with your email and password
2. **Login**: Access your account
3. **Add Transactions**: Use the form to add income or expenses
4. **Track Cash**: Update your actual cash in hand regularly
5. **Plan Ahead**: Add upcoming transactions to forecast your balance

### Adding Transactions

**Completed Transactions**:
- Select "Completed Expense (Debit)" or "Completed Income (Credit)"
- Choose the date
- Enter description and amount
- Submit to add to your ledger

**Upcoming Transactions**:
- Select "Upcoming Outgoing" or "Upcoming Receivable"
- No date needed (they appear at the bottom of your ledger)
- Enter description and amount
- Use "Complete" button to convert to a completed transaction

### Managing Cash
1. Count your actual cash in hand
2. Enter the amount in "Update Cash in Hand" form
3. Submit to record the snapshot
4. Compare with calculated balance to identify discrepancies

### Editing and Organizing
- Click "Edit" on any transaction to modify or delete it
- Use ↑↓ arrows to reorder transactions on the same day
- Reorder upcoming transactions to prioritize them

## Features Breakdown

### Statistics Dashboard
- **Current Balance**: Your calculated balance based on all transactions
- **Total Income**: Sum of all income entries (green)
- **Total Expenses**: Sum of all expense entries (red)
- **Cash in Hand**: Your actual cash amount (blue)

### Cash Reconciliation
- **Calculated Balance**: What your balance should be based on transactions
- **Actual Cash in Hand**: What you physically have
- **Untracked Cash Expenses**: The difference (helps identify missing transactions)

### Transaction Table
- Chronologically ordered transactions
- Running balance after each entry
- Color-coded amounts (green for income, red for expenses)
- Highlighted upcoming transactions in yellow
- Edit and reorder capabilities

## Security

- User authentication via Supabase Auth
- Row Level Security (RLS) should be enabled on all tables
- Each user can only access their own data
- Passwords are handled securely by Supabase

### Recommended RLS Policies

Enable RLS on all tables and add these policies:

```sql
-- For each table (transactions, upcoming_expenses, cash_history, user_settings)
-- Enable read access
CREATE POLICY "Users can view own records" ON table_name
    FOR SELECT USING (auth.uid() = user_id);

-- Enable insert access
CREATE POLICY "Users can insert own records" ON table_name
    FOR INSERT WITH CHECK (auth.uid() = user_id);

-- Enable update access
CREATE POLICY "Users can update own records" ON table_name
    FOR UPDATE USING (auth.uid() = user_id);

-- Enable delete access
CREATE POLICY "Users can delete own records" ON table_name
    FOR DELETE USING (auth.uid() = user_id);
```

## Browser Compatibility

- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)
- Mobile browsers (iOS Safari, Chrome Mobile)

## Contributing

This is a personal finance tracker project. Feel free to fork and customize for your needs!

## License

This project is open source and available for personal and commercial use.

## Support

For issues or questions, please open an issue in the repository.

## Future Enhancements

Potential features for future development:
- Categories and tags for transactions
- Budget tracking and alerts
- Data export (CSV, PDF)
- Charts and visualizations
- Recurring transaction templates
- Multiple currency support
- Bank account integration
- Receipt attachment capability

---

Made with care for personal finance management.
