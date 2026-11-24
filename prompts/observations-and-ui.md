# Student prompt — SafeMed Dashboard

Paste everything under the line into an AI session that has this repo open. The app already signs in and shows the Patient. This prompt adds patient-specific observations, allergy alerts, active prescriptions with dosage, and a medication safety reminder.

Students must sign out and sign in again after the scope change, or the new read is rejected.

---

I want my app to get observations and clean up the UI to follow industry standards.

Context you must keep:

- Patient-facing SMART on FHIR app. Static files only. GitHub Pages. No React, no build step, no backend, no new folder.
- Files: `launch.html` starts sign-in. `index.html` is the page after sign-in. Library already loaded: `https://cdn.jsdelivr.net/npm/fhirclient@3.0.1/bundle/fhir-client.js`.
- Sign-in already works. `FHIR.oauth2.authorize` in `launch.html`, `FHIR.oauth2.ready` then `client.patient.read()` in `index.html`. Keep that flow.
- Current scope in `launch.html` includes `launch/patient openid fhirUser patient/Patient.read patient/Observation.read`. Add `patient/AllergyIntolerance.read` and `patient/MedicationRequest.read` to that same string. Do not remove the existing scopes.
- FHIR R4. Reads only. Do not create or update resources.
- Do not fetch Condition or any resource besides Patient, Observation, AllergyIntolerance, and MedicationRequest. Read data only; do not create or update resources.

Observations to show, for the signed-in patient only:

- Height, LOINC `8302-2`
- Blood pressure. Prefer panel `85354-9` and read systolic `8480-6` and diastolic `8462-4` from `component`. If the server returns separate observations, use those codes.
- HDL, LOINC `2085-9`
- LDL, LOINC `13457-7`

Search observations with the patient id from the authorized client, not a hardcoded id. Request the codes above. If several results exist for one code, show the most recent by `effectiveDateTime` or `issued`. Read `valueQuantity` (value and unit). If a value is missing, show "Not recorded" for that row. If the search returns nothing, show an empty state that says the sandbox patient has no matching observations. A failed search shows a plain-language error and still shows the patient identity.

Allergy and medication tracker:

- Search `AllergyIntolerance` for the authorized patient only. Show recorded allergy substances, clinical status, and reaction details including severity when present. Do not imply that an empty allergy search confirms there are no allergies.
- Search `MedicationRequest` for the authorized patient only, filtered to active prescriptions. Show medication name and recorded dosage directions, including text, dose quantity, route, or timing when available. If dosage is absent, say it is not recorded and advise checking with a doctor or pharmacist.
- Show a medication safety reminder: take medicines as prescribed, check with a doctor or pharmacist before changing a dose or starting a medicine, and seek emergency help for a suspected severe allergic reaction. Make clear the app does not check drug interactions or replace professional advice.
- Give each tracker a loading state, useful empty state, and plain-language error. A failure in one tracker must not hide patient identity or the other sections.

UI, on `index.html` only:

- One page titled as the patient's record. Identity first (name, gender, birth date, age), then "Recent results", "Allergy alerts", "Active medications", and "Medication safety reminder" sections.
- Patient-facing labels: Height, Blood pressure, HDL, LDL. Blood pressure displays as systolic/diastolic with the unit.
- Show the number and the unit together. Show the date of the result when the resource has one.
- Keep name formatting defensive: FHIR `name` is a list; do not assume `name[0].given` exists.
- Loading state while data is in flight. Errors are text, not a blank page.
- Semantic HTML, visible labels, text and numbers not conveyed by color alone, readable body text, usable on a phone width. Static CSS in the page. No component library and no external stylesheet.
- Leave the sign-in and health-check links for the signed-out view. Leave sign-out working. After adding scopes, tell the student to sign out and sign in again.
- Comment each new function in plain language: what it requests, which field it reads, and what the student should see.

Also return:

- The files you changed
- The new scope string
- A short test list: sign in again to grant new scopes; confirm identity remains; confirm each observation row or its empty state; confirm allergy and active medication sections show data, empty states, or errors; confirm a patient with no matching data does not break the page.
