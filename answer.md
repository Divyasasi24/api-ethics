from datetime import datetime, timedelta

# Only keep fields needed for research — drop all Direct PII
REQUIRED_FIELDS = {"record_id", "age_group", "occupation_category", "diagnosis_code"}

RETENTION_DAYS = 90  # define a clear, justified retention window
expiry_date = datetime.utcnow() + timedelta(days=RETENTION_DAYS)

def minimize_record(record):
    """Strip all fields except those required for analysis."""
    return {k: v for k, v in record.items() if k in REQUIRED_FIELDS}

minimized_records = [minimize_record(r) for r in records]

# Store with full audit metadata
save_to_database(
    minimized_records,
    metadata={
        "purpose": "aggregate_health_trend_analysis",
        "collected_at": datetime.utcnow().isoformat(),
        "expires_at": expiry_date.isoformat(),
        "consent_basis": "api_terms_of_service_section_4",
        "data_controller": "healthstats-api.example.com"
    }
)
