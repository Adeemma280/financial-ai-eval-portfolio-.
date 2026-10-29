# financial-ai-eval-portfolio-.
"""
DCF Model Stress-Testing & AI Logic Benchmark
Author: Emmanuel Adeyemi
Description: A sample benchmarking script used to evaluate AI-generated 
             Discounted Cash Flow (DCF) model outputs for formula integrity, 
             Terminal Value bounding, and working capital sensitivity.
"""

def evaluate_dcf_output(fcf_projections, wacc, perpetual_growth_rate, terminal_year_fcf):
    """
    Evaluates whether an AI model's DCF logic meets domain accuracy standards.
    """
    issues = []
    
    # Rule 1: Perpetual growth rate bound check
    LONG_TERM_GDP_GROWTH = 0.03
    if perpetual_growth_rate > LONG_TERM_GDP_GROWTH:
        issues.append(
            f"CRITICAL ERROR: Perpetual growth rate ({perpetual_growth_rate:.1%}) "
            f"exceeds long-term GDP growth estimate ({LONG_TERM_GDP_GROWTH:.1%})."
        )
    
    # Rule 2: WACC sanity check
    if wacc <= 0 or wacc > 0.25:
        issues.append(
            f"WARNING: WACC rate ({wacc:.1%}) falls outside standard enterprise risk bounds."
        )
        
    # Rule 3: Terminal Value calculation
    if wacc <= perpetual_growth_rate:
        issues.append(
            "MATHEMATICAL IMPOSSIBILITY: WACC must be greater than perpetual growth rate "
            "to prevent negative or infinite Terminal Value."
        )
    else:
        terminal_value = (terminal_year_fcf * (1 + perpetual_growth_rate)) / (wacc - perpetual_growth_rate)
        print(f"Calculated Terminal Value: ${terminal_value:,.2f}")

    return {
        "is_valid": len(issues) == 0,
        "identified_issues": issues
    }

if __name__ == "__main__":
    # Test Scenario: Evaluating a flawed AI response
    sample_fcf = [100000, 115000, 125000, 140000, 150000]
    sample_wacc = 0.08
    flawed_growth_rate = 0.045  # Exceeds GDP growth
    
    print("--- Running AI Evaluation Benchmark ---")
    result = evaluate_dcf_output(sample_fcf, sample_wacc, flawed_growth_rate, sample_fcf[-1])
    
    print("\nBenchmark Evaluation Result:")
    for issue in result["identified_issues"]:
        print(f"- {issue}")
