# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Flask-based web application that displays course information. Simple MVC-style architecture with routes, view functions, and an in-memory Course model.

## Commands

```bash
# Setup
python -m venv venv && source venv/bin/activate  # Mac/Unix
pip install -r requirements.txt

# Run
python src/app.py  # http://127.0.0.1:5000

# Tests
python -m unittest discover -s tests          # all tests
python -m unittest tests.test_app             # single file
python -m unittest tests.test_app.AppTestCase.test_index  # single test
```

## Architecture

### Module Responsibilities

- **src/app.py**: Flask app setup. Routes are registered via `add_url_rule()` (not decorators), keeping views decoupled from Flask.
- **src/views.py**: Pure view functions — no Flask decorators. Imports only `render_template` and `request`. Course lookup uses `int(course_id) - 1` as list index (1-based IDs).
- **src/models.py**: `Course` class with `title`, `description`, `instructor`, `duration`, `topics`. All data hardcoded in a `courses` list — no database.
- **src/templates/**: Jinja2 templates. `layout.html` is the base with `{% block content %}`. Child templates use `{% extends 'layout.html' %}`.
- **src/static/css/styles.css**: Single stylesheet; uses CSS variables for design tokens.

### Routes

| Route | Methods | View |
|---|---|---|
| `/` | GET | `index` — passes full `courses` list |
| `/course/<course_id>` | GET | `course` — looks up by 1-based int index, returns 404 if not found |
| `/contact` | GET, POST | `contact` — validates name/email/address, prints submission to console |

### Known Template Quirk

`course.html` has redundant `<html>/<head>/<body>` tags alongside `{% extends 'layout.html' %}`. The extends/block content is what actually renders; the outer tags are ignored by Jinja2 but add noise. Don't replicate this pattern in new templates.

## Development Workflow

### Tests

Add unit tests for every change and confirm they pass before finishing. Tests live in `tests/test_app.py` and use Flask's built-in test client.

### Verify Changes with Playwright (MANDATORY)

**After implementing any new feature, you MUST:**

1. Start the Flask application (if not already running — `python src/app.py`)
2. Use the Playwright MCP tool to connect to the application at `http://127.0.0.1:5000`
3. Navigate to and interact with the new feature to verify it works correctly
4. Take a screenshot of the working feature
5. Save the screenshot in the `test-output/` folder with a descriptive filename (e.g., `feature-name-verification-YYYY-MM-DD.png`)

### Custom Skills

The `/ui-designer` skill handles all UI/UX changes. It follows a strict workflow: screenshot current state → analyze CSS → apply design system tokens → screenshot result. Invoke it for any styling or layout work.

Custom commands available: `/explain_this_file`, `/implement_ui_user_story`.
