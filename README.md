# Build Your Own Wrestling Team App (without being a programmer)

A walkthrough from Coach Ben Strandberg, Earlham Wrestling (Iowa).

I built our team an iPhone and iPad app that runs off the Google Sheet we already used for attendance and Ironman points. Wrestlers sign in with their school Google account, see their own stats, log their weight before and after practice, follow their descent plan, and sign the team handbook. Coaches take attendance, see everyone's weigh-ins, set weight classes, and get flagged when a kid is well over their plan.

I'm a coach, not a programmer. I built it with **Claude Code**, an AI coding assistant, by describing what I wanted in plain English. This guide is how you can do the same thing for your program.

---

## What you end up with

```
 Wrestler's / coach's phone              Google (your school account)
┌──────────────────────────┐   sign-in  ┌──────────────────────────────┐
│  Your team app           │──────────▶│  School Google sign-in        │
│  (iPhone + iPad)         │            └──────────────────────────────┘
│                          │   asks for  ┌──────────────────────────────┐
│                          │──────────▶│  Google Apps Script           │
│                          │   own data  │  (checks who's asking, then  │
└──────────────────────────┘            │   reads/writes the sheet)     │
                                         └──────────────┬───────────────┘
                                                        ▼
                                         ┌──────────────────────────────┐
                                         │  Your team Google Sheet       │
                                         │  (coaches keep editing it)    │
                                         └──────────────────────────────┘
```

- **The Google Sheet stays the boss.** Coaches keep using it like always. The app reads from it and adds rows to it.
- **A small Google Apps Script** sits between the app and the sheet. It checks the school Google sign-in on every request, so a wrestler only ever gets *their own* data.
- **The app** is a real iPhone/iPad app, on the App Store as an *unlisted* app (only people with the link can find it).

## What you need

| Thing | Cost | Notes |
|---|---|---|
| A Mac | – | Needed to build iPhone apps (Xcode only runs on a Mac). |
| Xcode | Free | From the Mac App Store. |
| Claude Code | Claude subscription | The AI assistant that writes and tests the code. |
| Apple Developer account | $99/year | To put the app on the App Store and on your own devices. |
| Your team's Google Sheet | Free | With a school email for each wrestler. |
| School Google (Workspace) account | – | So sign-in can be limited to your school's accounts. |
| Time | A few focused days | Most of my time was deciding what I wanted and testing it. |

Talk to your **district IT** early. School Google accounts for students under 18 can be blocked from apps the district hasn't approved. Ours worked without IT changing anything, but yours might need them to approve the app's sign-in.

---

## Step by step

### 1. Get your sheet in shape
**Start from my blank templates** (they open as your own private copy):
- [Ironman Tracker template](https://docs.google.com/spreadsheets/d/1lxp2jwKBXsLiLAjSiXcn-f-m57zZiXmrFUF_78pF9Ws/copy): Dashboard with all the point and Ironman formulas, Roster, Portal Access, Practice, Open Mat, Kids Club, Settings, Handbook Sign-Off and Descent Plans. Read its **START HERE** tab first, and set your school's email domain on **Settings**.
- [Weight Class Plans template](https://docs.google.com/spreadsheets/d/1Kin2CT8FNV5F2sYEvteUeH_J8yMWZz2kewNhZxP8fPA/copy): before/after Christmas weight class choices.

Keep your copy private. It will hold minors' attendance and weights.

If you'd rather use your own sheet, it needs, at minimum:
- A **roster** tab with each wrestler's name.
- A tab that maps **wrestler name → school email → role** (wrestler or coach). This is what decides who sees what.
- Whatever you already track: attendance, points, weight plans. Don't restructure it for the app; the app should fit your sheet, not the other way around.

### 2. Set up Claude Code and a project folder
1. Install Claude Code (search "Claude Code" on claude.com) and open it in a new, empty folder for your app.
2. Write a short note telling it what you're building. Ours started like this:

> Turn our team's wrestler portal into a native iPhone app (Xcode / SwiftUI), distributed through the App Store as an unlisted app. It has to keep using our current Google Sheet as the data; coaches keep editing the sheet as they do now. Wrestlers sign in with their school Google account and can log their own weight; each weigh-in is added to the sheet as a new row. Wrestlers must only ever see their own data. These are minors' weights, so keep the sheet private.

3. Let it ask you questions. Answer them. It will keep notes for the next session.

### 3. The connection between the app and the sheet (Google Apps Script)
Ask Claude Code to write the Apps Script for you. In the sheet: **Extensions → Apps Script**, paste in what it gives you, then **Deploy → New deployment → Web app**.

Things that mattered:
- **Who can see what is decided on the server, not in the app.** The script checks the Google sign-in and the email domain on every request.
- **If you already have a website portal on the same script, keep its deployment separate** and don't update it until you mean to. A second deployment just for the app keeps them independent.
- Every time the script changes, it needs a **new deployment version** before the app sees it. (This trips everyone up once.)
- Ask for a **test file** that runs the script against fake data. Ours has 70+ checks, things like "a wrestler can't read another wrestler's weigh-ins" and "only coaches can take attendance."

### 4. School Google sign-in
In Google Cloud (signed in with your **school** account, not a personal Gmail):
1. Create a project.
2. Set up the sign-in screen with the audience set to **Internal**, so only your school's accounts can use it.
3. Create an **iOS** sign-in client with your app's bundle ID.

Claude Code can walk you through each screen. Test sign-in with a real student account early, before you build much else, because that's where a district block would show up.

### 5. Build the app with fake data first
Have it build the app with a **sample data mode** (made-up wrestlers) so you can try every screen in the iPhone/iPad Simulator before it touches the real sheet. This is also how you make App Store screenshots without showing real kids.

### 6. Connect it to the real sheet and test
- Make a **test wrestler account** (a school account that's on the roster) for yourself and for Apple's reviewers.
- Try it as a wrestler and as a coach.
- Put it on your own phone and iPad: plug in, turn on **Developer Mode** (Settings → Privacy & Security), and have Claude Code install it. With a paid developer account these test installs last a year.

### 7. The App Store
Claude Code can fill most of this in with you, but you click the final buttons:
- Create the app in **App Store Connect**, with a name, description, category and age rating.
- A **privacy policy page** (we put ours on our team website) and the **App Privacy** answers: name, email, fitness data; linked to the user; not used for tracking.
- **Screenshots** in the sizes Apple asks for (it can take these from the Simulator).
- **App Review info:** the test wrestler account, plus notes explaining that it's for one school team and uses school accounts. Sign in with Apple isn't required for apps that only use existing school accounts.
- Submit. Review took us about a day.
- Then send Apple's **unlisted app distribution request**, so the app isn't searchable and you share it by link.

### 8. Keep improving it
After the first version, every change was a short conversation:

> When I click a name on the Descent tab I want it to go right to the wrestler's plan.

> I want to punch in their weights if I weigh them in that day. I also want to be able to change one of their entries if it isn't correct.

> Wrestlers won't be on track during the week; descent plans are a guide for meets. Remove the warnings when they enter weights, and on the coach side show me if they're more than 5 lbs over.

It changes the code, tests it, and installs it on your devices. When you're happy, it builds a new version for the App Store.

---

## Prompts that worked well
- "Run a test on the iPad coach side and tell me what else could be better." It came back with six ideas, and I said "do all 6."
- "Any other things to change or check before ship?" It gave a checklist split into what it could do and what I had to check myself.
- "Names on the list should be alphabetical by last name."
- "I want a dark mode too."
- "Make it just Power/Speed, no need for 'morning'." Small wording changes are fine to ask for.

## Things I learned
- **Decide what you want first.** The code was the easy part. Knowing how *my* team runs (before and after practice weigh-ins, descent plans as a guide, late vs. excused) was the real work.
- **Protect the kids' data.** Weights and attendance of minors stay in a private sheet. The app only ever shows a wrestler their own information, and screenshots use made-up names.
- **Keep the sheet as the source of truth.** Coaches who don't use the app can keep using the sheet.
- **Test on real devices** (your phone and an iPad) before the team gets it.
- **Updates go through Apple review** (about a day), but anything in the sheet shows up right away.

## Costs (ours)
- Apple Developer: **$99/year**
- Google: **free** (school Workspace account)
- Claude: a **Claude subscription**
- Everything else: free

---

I'm also working on a practice scheduler (live now), and next on a version of this app that any team can set up without building their own. If you have questions, reach out. Happy to help another program get going.
