# HFA-Governance-System
import streamlit as st
import pandas as pd
import sqlite3
import json
from datetime import datetime

# =====================================
# HUMAN FIRST AI - HFA
# =====================================

st.set_page_config(
    page_title="HFA | Human First AI",
    page_icon="🛡️",
    layout="wide"
)

st.title("🛡️ HUMAN FIRST AI")
st.subheader("AI Governance & Human Protection System")

st.write(
    "A system for monitoring AI usage, "
    "evaluating risks, detecting violations "
    "and recommending human-centered safeguards."
)

# =====================================
# PART 1: GOVERNANCE FRAMEWORK
# =====================================

governance_framework = {
    "Privacy": 20,
    "Fairness": 20,
    "Safety": 25,
    "Transparency": 15,
    "Human Oversight": 20
}

# =====================================
# PART 2: VIOLATION RULES
# =====================================

violation_rules = {
    "privacy_violation": {
        "name": "Privacy Violation",
        "severity": "HIGH",
        "recommendation": "Review data protection procedures."
    },
    "bias_detected": {
        "name": "Potential Bias",
        "severity": "HIGH",
        "recommendation": "Conduct a fairness assessment."
    },
    "harmful_output": {
        "name": "Harmful Output",
        "severity": "CRITICAL",
        "recommendation": "Require urgent human safety review."
    },
    "transparency_missing": {
        "name": "Transparency Issue",
        "severity": "MEDIUM",
        "recommendation": "Improve transparency and explainability."
    },
    "human_oversight_missing": {
        "name": "Missing Human Oversight",
        "severity": "HIGH",
        "recommendation": "Require qualified human review."
    },
    "repeated_violations": {
        "name": "Repeated Violations",
        "severity": "HIGH",
        "recommendation": "Conduct a compliance investigation."
    }
}

# =====================================
# PART 3: COMPLIANCE ENGINE
# =====================================

def analyze_case(record):

    violations = []

    checks = [
        "privacy_violation",
        "bias_detected",
        "harmful_output",
        "transparency_missing"
    ]

    for rule in checks:
        if record[rule]:
            violations.append(rule)

    if (
        record["human_review_required"]
        and not record["human_review_done"]
    ):
        violations.append("human_oversight_missing")

    if record["previous_violations"] >= 3:
        violations.append("repeated_violations")

    severity_points = {
        "MEDIUM": 2,
        "HIGH": 3,
        "CRITICAL": 5
    }

    risk_score = sum(
        severity_points[violation_rules[v]["severity"]]
        for v in violations
    )

    if risk_score == 0:
        risk_level = "LOW"
        action = "ALLOW"
        decision = "CONTINUE WITH MONITORING"

    elif risk_score <= 3:
        risk_level = "MEDIUM"
        action = "WARNING_AND_MONITORING"
        decision = "WARNING AND CONTINUED MONITORING"

    elif risk_score <= 7:
        risk_level = "HIGH"
        action = "RESTRICT_RELEVANT_FUNCTION_PENDING_REVIEW"
        decision = "REVIEW AND PROPORTIONATE RESTRICTION"

    else:
        risk_level = "CRITICAL"
        action = "ESCALATE_FOR_URGENT_HUMAN_REVIEW"
        decision = "URGENT HUMAN REVIEW"

    alerts = [
        violation_rules[v]["severity"] + ": "
        + violation_rules[v]["name"]
        for v in violations
    ]

    recommendations = [
        violation_rules[v]["recommendation"]
        for v in violations
    ]

    if not violations:
        alerts = ["No violation detected."]
        recommendations = [
            "Maintain current governance standards."
        ]

    return {
        "violations": violations,
        "risk_score": risk_score,
        "risk_level": risk_level,
        "compliance_status": (
            "COMPLIANT" if not violations
            else "NON-COMPLIANT"
        ),
        "action": action,
        "decision": decision,
        "alerts": alerts,
        "recommendations": recommendations
    }

# =====================================
# PART 4: SAMPLE DATA
# =====================================

sample_data = [
    {
        "case_id": 1, "system": "AI System A",
        "privacy_violation": False, "bias_detected": False,
        "harmful_output": False, "transparency_missing": False,
        "human_review_required": False, "human_review_done": True,
        "previous_violations": 0
    },
    {
        "case_id": 2, "system": "AI System B",
        "privacy_violation": True, "bias_detected": True,
        "harmful_output": False, "transparency_missing": True,
        "human_review_required": True, "human_review_done": False,
        "previous_violations": 1
    },
    {
        "case_id": 3, "system": "AI System C",
        "privacy_violation": False, "bias_detected": False,
        "harmful_output": True, "transparency_missing": False,
        "human_review_required": True, "human_review_done": False,
        "previous_violations": 3
    },
    {
        "case_id": 4, "system": "AI System D",
        "privacy_violation": False, "bias_detected": False,
        "harmful_output": False, "transparency_missing": True,
        "human_review_required": False, "human_review_done": True,
        "previous_violations": 0
    },
    {
        "case_id": 5, "system": "AI System E",
        "privacy_violation": True, "bias_detected": True,
        "harmful_output": True, "transparency_missing": True,
        "human_review_required": True, "human_review_done": True,
        "previous_violations": 2
    },
    {
        "case_id": 6, "system": "AI System F",
        "privacy_violation": False, "bias_detected": False,
        "harmful_output": False, "transparency_missing": False,
        "human_review_required": True, "human_review_done": True,
        "previous_violations": 0
    }
]

# =====================================
# PART 5: ANALYZE SAMPLE CASES
# =====================================

results = []

for record in sample_data:
    result = analyze_case(record)

    results.append({
        "Case": record["case_id"],
        "AI System": record["system"],
        "Compliance": result["compliance_status"],
        "Risk Score": result["risk_score"],
        "Risk Level": result["risk_level"],
        "Decision": result["decision"]
    })

results_df = pd.DataFrame(results)

# =====================================
# PART 6: FAIRNESS ASSESSMENT
# =====================================

group_rates = {
    "Group A": 0.60,
    "Group B": 0.35
}

difference = abs(
    group_rates["Group A"] -
    group_rates["Group B"]
)

if difference <= 0.05:
    fairness_result = "LOW OBSERVED DIFFERENCE"
elif difference <= 0.10:
    fairness_result = "MODERATE OBSERVED DIFFERENCE"
else:
    fairness_result = "HIGH OBSERVED DIFFERENCE"

# =====================================
# PART 7: DATABASE
# =====================================

def save_case(record, result):

    connection = sqlite3.connect("hfa_database.db")

    connection.execute("""
        CREATE TABLE IF NOT EXISTS cases (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            system_name TEXT,
            risk_level TEXT,
            risk_score INTEGER,
            compliance_status TEXT,
            decision TEXT,
            details TEXT,
            created_at TEXT
        )
    """)

    connection.execute("""
        INSERT INTO cases
        (system_name, risk_level, risk_score,
         compliance_status, decision, details, created_at)
        VALUES (?, ?, ?, ?, ?, ?, ?)
    """, (
        record["system"],
        result["risk_level"],
        result["risk_score"],
        result["compliance_status"],
        result["decision"],
        json.dumps(result["violations"]),
        datetime.now().isoformat()
    ))

    connection.commit()
    connection.close()

# =====================================
# USER INTERFACE
# =====================================

st.sidebar.title("HFA Navigation")

page = st.sidebar.radio(
    "Choose a section",
    [
        "Dashboard",
        "Governance Framework",
        "Analyze AI System",
        "Fairness Assessment",
        "Reports"
    ]
)

# =====================================
# DASHBOARD
# =====================================

if page == "Dashboard":

    st.header("Governance Dashboard")

    total = len(results_df)
    compliant = (
        results_df["Compliance"] == "COMPLIANT"
    ).sum()

    non_compliant = total - compliant

    col1, col2, col3 = st.columns(3)

    col1.metric("Total Cases", total)
    col2.metric("Compliant Cases", int(compliant))
    col3.metric("Non-Compliant Cases", int(non_compliant))

    st.subheader("Risk Distribution")

    st.bar_chart(
        results_df["Risk Level"].value_counts()
    )

    st.subheader("Monitoring Results")

    st.dataframe(
        results_df,
        use_container_width=True
    )

# =====================================
# GOVERNANCE FRAMEWORK
# =====================================

elif page == "Governance Framework":

    st.header("Governance Framework")

    framework_df = pd.DataFrame(
        list(governance_framework.items()),
        columns=["Governance Principle", "Weight (%)"]
    )

    st.dataframe(
        framework_df,
        use_container_width=True
    )

    st.write(
        "Total Weight:",
        sum(governance_framework.values()),
        "%"
    )

# =====================================
# ANALYZE AI SYSTEM
# =====================================

elif page == "Analyze AI System":

    st.header("AI System Risk Assessment")

    with st.form("assessment_form"):

        system_name = st.text_input(
            "AI System Name",
            placeholder="Enter system name"
        )

        st.subheader("Compliance Checks")

        privacy = st.checkbox("Privacy violation detected")
        bias = st.checkbox("Potential bias detected")
        harmful = st.checkbox("Harmful output detected")
        transparency = st.checkbox("Transparency information missing")

        human_required = st.checkbox(
            "Human review is required"
        )

        human_done = st.checkbox(
            "Human review has been completed"
        )

        previous = st.number_input(
            "Previous violations",
            min_value=0,
            step=1
        )

        submitted = st.form_submit_button(
            "Analyze AI System"
        )

    if submitted:

        if not system_name.strip():
            st.error("Please enter an AI system name.")

        else:

            record = {
                "system": system_name,
                "privacy_violation": privacy,
                "bias_detected": bias,
                "harmful_output": harmful,
                "transparency_missing": transparency,
                "human_review_required": human_required,
                "human_review_done": human_done,
                "previous_violations": int(previous)
            }

            result = analyze_case(record)
            save_case(record, result)

            st.subheader("Assessment Results")

            st.metric(
                "Risk Score",
                result["risk_score"]
            )

            st.write(
                "**Risk Level:**",
                result["risk_level"]
            )

            st.write(
                "**Compliance Status:**",
                result["compliance_status"]
            )

            st.write(
                "**Recommended Action:**",
                result["action"]
            )

            st.write(
                "**Governance Decision:**",
                result["decision"]
            )

            st.subheader("Detected Violations")

            for alert in result["alerts"]:
                st.write("- " + alert)

            st.subheader("Recommendations")

            for recommendation in result["recommendations"]:
                st.write("- " + recommendation)

            st.success(
                "Assessment saved to HFA database."
            )

# =====================================
# FAIRNESS ASSESSMENT
# =====================================

elif page == "Fairness Assessment":

    st.header("Fairness and Bias Assessment")

    fairness_df = pd.DataFrame(
        list(group_rates.items()),
        columns=["Group", "Selection Rate"]
    )

    st.bar_chart(
        fairness_df.set_index("Group")
    )

    st.metric(
        "Selection Rate Difference",
        f"{difference:.2%}"
    )

    st.write(
        "**Fairness Assessment:**",
        fairness_result
    )

    st.warning(
        "This is an illustrative test dataset. "
        "A difference alone does not prove discrimination."
    )

# =====================================
# REPORTS
# =====================================

elif page == "Reports":

    st.header("HFA Reports")

    st.subheader("Compliance Report")

    st.dataframe(
        results_df,
        use_container_width=True
    )

    csv_data = results_df.to_csv(index=False).encode("utf-8")

    st.download_button(
        "Download Compliance Report",
        data=csv_data,
        file_name="HFA_Compliance_Report.csv",
        mime="text/csv"
    )

    st.subheader("Governance Summary")

    st.write("Project: HUMAN FIRST AI")
    st.write("Version: 1.0")
    st.write("Total Test Cases:", len(results_df))
    st.write("Fairness:", fairness_result)

    st.write(
        "Generated at:",
        datetime.now().strftime("%Y-%m-%d %H:%M")
    )

# =====================================
# FOOTER
# =====================================

st.divider()

st.caption(
    "HUMAN FIRST AI (HFA) | "
    "Human-Centered AI Governance | Version 1.0"
)
