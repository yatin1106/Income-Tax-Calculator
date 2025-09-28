


import gradio as gr

def calculate_tax_new_regime(salary, pf, gratuity, medical, other_income, manual_deductions, apply_cess):
   

    
    try:
        salary = float(salary or 0)
        pf = float(pf or 0)
        gratuity = float(gratuity or 0)
        medical = float(medical or 0)
        other_income = float(other_income or 0)
        deductions = float(manual_deductions or 0)
    except Exception as e:
        return f"Input error: please enter valid numeric values. ({e})"

    # --- Compute gross and taxable income
    gross_income = salary + pf + gratuity + medical + other_income
    taxable = gross_income - deductions
    if taxable < 0:
        taxable = 0

    # --- New regime slabs (simplified)
    tax = 0.0
    if taxable <= 300000:
        tax = 0.0
    elif taxable <= 600000:
        tax = (taxable - 300000) * 0.05
    elif taxable <= 900000:
        tax = 15000 + (taxable - 600000) * 0.10
    elif taxable <= 1200000:
        tax = 45000 + (taxable - 900000) * 0.15
    elif taxable <= 1500000:
        tax = 90000 + (taxable - 1200000) * 0.20
    else:
        tax = 150000 + (taxable - 1500000) * 0.30

    # --- Cess (4%) optional
    cess = tax * 0.04 if apply_cess else 0.0
    total_tax = tax + cess

    # --- Prepare nicely formatted output
    out = (
        f"Gross Income: ₹{gross_income:,.2f}\n"
        f"Taxable Income: ₹{taxable:,.2f}\n"
        f"Tax before cess: ₹{tax:,.2f}\n"
        f"Cess (4%): ₹{cess:,.2f}\n"
        f"Total Tax Liability: ₹{total_tax:,.2f}"
    )
    return out


with gr.Blocks() as demo:
    gr.Markdown("# Income Tax Calculator — New Regime (Simple)")

    with gr.Row():
        salary = gr.Number(label="Salary (annual, ₹)", value=500000)
        pf = gr.Number(label="Provident Fund (PF)", value=0)

    with gr.Row():
        gratuity = gr.Number(label="Gratuity", value=0)
        medical = gr.Number(label="Medical Allowance", value=0)

    other_income = gr.Number(label="Other Income", value=0)
    manual_deductions = gr.Number(label="Manual Deductions (if any)", value=0)
    apply_cess = gr.Checkbox(label="Apply 4% Health & Education Cess", value=True)

    output = gr.Textbox(label="Result", lines=6)
    calc_btn = gr.Button("Calculate")

    calc_btn.click(
        calculate_tax_new_regime,
        inputs=[salary, pf, gratuity, medical, other_income, manual_deductions, apply_cess],
        outputs=output
    )

demo.launch()
