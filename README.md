# Discord Quiz Bot

A Discord trivia bot built with discord.js v14.

The bot supports multiple choice quizzes, timed answers, custom question banks, daily participation limits, persistent leaderboards, weekly results, and a self hosted web app for checking daily quiz results.

## Features

- `/quiz start` starts a quiz with 1 to 10 questions.
- `/quiz stop` stops the active quiz in the channel.
- Questions use four choices: A, B, C, and D.
- Each question has a 10 second answer timer.
- Each player can submit one answer per question.
- Answers are private and the correct option is not revealed after the round.
- Players earn points for correct and fast answers.
- The first correct player receives an additional bonus.
- Each member can play one quiz session per day.
- `/leaderboard` shows the all time leaderboard.
- `/leaderboard period:weekly` shows the weekly leaderboard.
- Server administrators can add, edit, remove, list, and bulk import questions.
- Questions can be imported from JSON or CSV files.
- The bot stores scores and quiz data in JSON files.
- The bot includes a web app for checking daily quiz results.
- The web app and API are served by the same Node.js application.

## Requirements

- Node.js 18 or newer
- A Discord application and bot
- A Discord server where the bot can be installed

## Installation

Clone or copy the project to your server.

```bash
cd discord-quiz-bot
npm install
```

Create the environment file:

```bash
cp .env.example .env
```

Edit the file:

```bash
nano .env
```

Add your Discord credentials:

```env
DISCORD_TOKEN=your_bot_token_here
CLIENT_ID=your_application_id_here
GUILD_ID=your_server_id
API_PORT=8787
```

Save the file in nano with:

```text
Ctrl + O
Enter
Ctrl + X
```

## Discord Application Setup

Create an application in the Discord Developer Portal.

You will need:

- Bot token
- Application ID
- Server ID

Enable the following bot permissions:

- Send Messages
- Embed Links
- Read Message History
- Use Slash Commands

The bot also needs the `bot` and `applications.commands` OAuth2 scopes.

## Register Slash Commands

After configuring `.env`, run:

```bash
npm run deploy
```

If `GUILD_ID` is set, commands are registered for that server and normally appear immediately.

If `GUILD_ID` is empty, commands are registered globally and may take longer to appear.

## Start the Bot

Run:

```bash
npm start
```

A successful startup should show a message similar to:

```text
Logged in as YourBot
```

## Commands

### Quiz

```text
/quiz start
/quiz start rounds:10
/quiz start rounds:10 source:mixed
/quiz stop
```

Quiz sources can include:

- Open Trivia DB
- Custom questions
- Mixed sources

API questions can also use category and difficulty filters.

### Leaderboard

```text
/leaderboard
/leaderboard period:weekly
```

Administrators can reset the all time leaderboard:

```text
/leaderboard reset:true
```

### Question Management

Add a question:

```text
/addquestion
```

Edit a question:

```text
/editquestion
```

Remove a question:

```text
/removequestion
```

List questions:

```text
/listquestions
```

Import questions:

```text
/importquestions
```

The question management commands require the Manage Server permission.

## Bulk Question Import

Questions can be imported from JSON or CSV files.

Templates are included in:

```text
templates/questions-template.json
templates/questions-template.csv
```

### CSV Format

The CSV file must contain these columns:

```text
question,option_a,option_b,option_c,option_d,correct,category
```

Example:

```csv
question,option_a,option_b,option_c,option_d,correct,category
What is the native token of the Concrete ecosystem?,CONC,ETH,SOL,BTC,A,Concrete
```

The `correct` value must be A, B, C, or D.

The category is optional. If it is empty, the bot uses `Custom`.

### JSON Format

JSON uses an array of question objects:

```json
[
  {
    "question": "What is the native token of the Concrete ecosystem?",
    "option_a": "CONC",
    "option_b": "ETH",
    "option_c": "SOL",
    "option_d": "BTC",
    "correct": "A",
    "category": "Concrete"
  }
]
```

Invalid rows are skipped and reported while valid rows are still imported.

## Scoring

The quiz uses the following scoring model:

- 100 base points for a correct answer
- Up to 50 additional points based on answer speed
- 25 bonus points for the first correct player in a round
- Wrong answers receive 0 points
- Missed answers receive 0 points

## Daily Participation

A member can start one quiz session per day.

The daily limit prevents players from replaying the quiz to improve their score.

The limit does not interrupt the remaining questions in the same quiz session.

Daily participation is tracked using UTC dates.

## Web App and API

The bot serves the web app from:

```text
public/index.html
```

The API server also provides daily quiz results.

Endpoint:

```text
GET /api/daily-result?username=<discord_username>&date=<YYYY-MM-DD>
```

The `username` parameter is required.

The `date` parameter is optional and defaults to the current UTC date.

Example:

```text
/api/daily-result?username=Switch
```

The API returns information such as:

- Username
- Date
- Points
- Correct answers
- Questions played
- Rank
- Total participants

The API is limited to the guild configured in `GUILD_ID`.

## Hosting

The bot can serve both the Discord bot and the web app from the same deployment.

Platforms such as Railway, Render, or a VPS can be used.

The application uses the value of `API_PORT` for the web server.

For production deployments, expose the application through HTTPS.

## Data Storage

The bot stores runtime data in the `data` directory.

```text
data/
  scores.json
  customQuestions.json
  dailyResults.json
  weeklyResults.json
  dailyParticipation.json
```

No database is required.

For larger deployments, the storage layer can later be replaced with SQLite, PostgreSQL, or another database.

## Project Structure

```text
discord-quiz-bot/
|
|-- index.js
|-- deploy-commands.js
|-- package.json
|-- .env.example
|
|-- commands/
|   |-- quiz.js
|   |-- leaderboard.js
|   |-- addquestion.js
|   |-- importquestions.js
|   |-- editquestion.js
|   |-- listquestions.js
|   |-- removequestion.js
|   |-- checkwallet.js
|
|-- lib/
|   |-- quizManager.js
|   |-- triviaAPI.js
|   |-- customQuestions.js
|   |-- questionImporter.js
|   |-- scores.js
|   |-- dailyResults.js
|   |-- weeklyResults.js
|   |-- dailyParticipation.js
|   |-- apiServer.js
|   |-- chainScanner.js
|   |-- store.js
|
|-- public/
|   |-- index.html
|
|-- templates/
|   |-- questions-template.json
|   |-- questions-template.csv
|
|-- data/
    |-- scores.json
    |-- customQuestions.json
    |-- dailyResults.json
    |-- weeklyResults.json
    |-- dailyParticipation.json
```

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `DISCORD_TOKEN` | Yes | Discord bot token |
| `CLIENT_ID` | Yes | Discord application ID |
| `GUILD_ID` | Recommended | Server ID for instant command registration and API scope |
| `API_PORT` | No | Port used by the web app and API |

## Development

Install dependencies:

```bash
npm install
```

Register commands:

```bash
npm run deploy
```

Start the bot:

```bash
npm start
```

## Security

Never commit the `.env` file to Git.

Keep these values private:

```text
DISCORD_TOKEN
CLIENT_ID
GUILD_ID
```

The `.env.example` file is safe to commit because it contains placeholders rather than real credentials.

## Troubleshooting

### Missing Discord token

If you see:

```text
Missing DISCORD_TOKEN in your .env file.
```

Make sure `.env` exists and contains:

```env
DISCORD_TOKEN=your_real_bot_token
```

Check the file with:

```bash
cat .env
```

Do not share the token with anyone.

### Slash commands are not appearing

Run:

```bash
npm run deploy
```

Make sure `CLIENT_ID` is correct.

For faster development, set:

```env
GUILD_ID=your_server_id
```

Then deploy the commands again.

### Check the current directory

Run:

```bash
pwd
```

You should see:

```text
/data/data/com.termux/files/home/discord-quiz-bot
```

Then:

```bash
ls -la
```

## License

Add your preferred license here before publishing the project.
