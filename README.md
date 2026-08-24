# Movie-Alert

This watcher sends a Telegram message when the Cinema City calendar changes
for a movie at a selected cinema. The current configuration monitors **The
Odyssey at Praha Flora, OC FLORA**.

## How it works

The poller calls Cinema City's calendar API for movie `7268s2r` and cinema
`1052`. It stores the returned date list in `state.json` and sends one message
when dates are added or removed. Dates that naturally expire because the
calendar moves to the next day are ignored.

The first run saves a baseline and does not send a notification. The GitHub
Actions workflow persists the updated state file after each run.

## Telegram setup

1. Message `@BotFather`, create a bot, and copy its token.
2. Send the bot a message.
3. Open `https://api.telegram.org/bot<TOKEN>/getUpdates` and copy the
   `chat.id` value.
4. Add `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID` as GitHub repository secrets.

## Configuration

The target is configured in `config.json`:

- `movie_id`: Cinema City film ID (`7268s2r` for The Odyssey).
- `cinema_id`: Cinema City cinema ID (`1052` for Praha Flora).
- `calendar_url_template`: the calendar API endpoint; `{until}` is replaced
  with today's date plus `days_in_advance`.
- `target_url`: the human-facing Cinema City booking page included in alerts.

After changing the movie or cinema, delete the `calendar_dates` value from
`state.json` (or set the file to `{}`) so the next run creates a fresh
baseline.

## Running locally

```text
pip install -r requirements.txt
set TELEGRAM_BOT_TOKEN=your-token
set TELEGRAM_CHAT_ID=your-chat-id
python poller.py
```

The GitHub Actions workflow can be triggered manually from the Actions tab.
It is also ready for an external cron trigger if regular polling is needed.

## Project files

| File | Purpose |
| --- | --- |
| `poller.py` | Fetches the calendar, compares state, and sends Telegram alerts |
| `config.json` | Movie, cinema, API, and booking-page settings |
| `state.json` | Automatically maintained last-seen calendar |
| `.github/workflows/booking-watch.yml` | GitHub Actions runner |
