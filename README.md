Travel Agent Training Simulator
This project is a lightweight, web-based training simulator designed for travel back-office agents. It allows admins to build "click-through" training scenarios and agents to practice them in a realistic booking system environment.
🚀 Quick Start
1. Database Setup (Supabase)
Create a new project in Supabase.
Go to the SQL Editor and run the following script to create the necessary tables:
code
SQL
CREATE TABLE scenarios (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title TEXT NOT NULL,
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE scenario_steps (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scenario_id UUID REFERENCES scenarios(id) ON DELETE CASCADE,
    step_order INTEGER NOT NULL,
    step_type TEXT NOT NULL,
    instruction TEXT,
    expected_value TEXT,
    hint_text TEXT
);

CREATE TABLE scenario_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    scenario_id UUID REFERENCES scenarios(id) ON DELETE CASCADE,
    agent_name TEXT,
    started_at TIMESTAMPTZ DEFAULT NOW(),
    completed_at TIMESTAMPTZ,
    mistakes_count INTEGER DEFAULT 0,
    status TEXT DEFAULT 'in_progress'
);
2. Configure the Files
Open admin.html and training.html in a text editor. Locate the following lines at the start of the <script> tag and replace them with your Supabase credentials:
code
JavaScript
const SUPABASE_URL = "YOUR_SUPABASE_PROJECT_URL";
const SUPABASE_KEY = "YOUR_SUPABASE_ANON_KEY";
3. File Structure
admin.html: The Scenario Builder. Use this to create training modules.
training.html: The Agent Simulator. Use this to play through the training.
🛠 How to Use
Admin - Creating a Scenario
Open admin.html in your browser.
Click + New Scenario, give it a title (e.g., "Handle Flight Refund") and a description.
Click on the scenario in the sidebar to open the Step Builder.
Add steps in order. Each step requires:
Type: What element the agent must interact with.
Instruction: What the agent sees (e.g., "Enter the PNR code ABC123").
Expected Value: The exact answer required to pass the step.
Hint: Text shown if the agent clicks the "Hint" button.
Agent - Playing a Scenario
Open training.html.
Enter your name and select a scenario from the list.
Follow the instructions in the bottom light-blue panel.
Validation: If you perform the correct action, you move to the next step. If you make a mistake, the screen flashes red, and you are reset to the beginning of the scenario to try again.
📖 Supported Step Types
Step Type	Required Action
fill_external_reference	Enter text into the "External Reference" field.
attach_pdf	Upload any file to the "Attachment" input.
click_save_details	Click the blue "Save Details" button.
edit_fare_name	Click Itinerary Edit -> Enter specific Fare Name in modal.
edit_pnr	Click Itinerary Edit -> Enter specific PNR in modal.
click_update_payment	Click "Update Payment" -> Check the checkbox -> Click Update.
click_charge_and_voice	Click the "Charge and Voice" button in the top bar.
send_confirmation	Click the "Re-send Confirmation" button.
📊 Monitoring Progress
You can view agent performance directly in the Supabase Dashboard under the scenario_sessions table. It tracks:
agent_name: Who took the training.
mistakes_count: How many times they failed a step.
status: Whether they finished (completed) or quit (in_progress).
completed_at: Time of completion.
