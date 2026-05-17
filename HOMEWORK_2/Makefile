ASK ?= ./ask
CODEBASE ?= codebase.txt

.PHONY: all clean

all: action.plan.md

# Phase 1 - FAN-OUT (parallel)
quality.md: $(CODEBASE)
	@cat $(CODEBASE) | $(ASK) "You are a senior code reviewer. Analyze ONLY code quality: readability, structure, naming, modularity, duplication, maintainability. Output exactly 5-7 bullet points as 'problem -> fix'. Keep each bullet actionable and concise." > $@

perf.md: $(CODEBASE)
	@cat $(CODEBASE) | $(ASK) "You are a performance engineer. Analyze ONLY performance: bottlenecks, algorithmic inefficiencies, memory/IO hotspots, unnecessary work. Output exactly 5-7 bullet points as 'issue -> optimization'. Keep each bullet actionable and concise." > $@

security.md: $(CODEBASE)
	@cat $(CODEBASE) | $(ASK) "You are an application security reviewer. Analyze ONLY security: vulnerabilities, unsafe patterns, missing validation, secret handling, auth/authz issues. Output exactly 5-7 bullet points as 'risk -> mitigation'. Keep each bullet actionable and concise." > $@

# Phase 2 - LOCAL SUMMARIZATION
quality.sum.md: quality.md
	@cat quality.md | $(ASK) "Summarize into EXACTLY 5 bullets. Keep only actionable code quality items. Format as '- ...' and keep each bullet short." > $@

perf.sum.md: perf.md
	@cat perf.md | $(ASK) "Summarize into EXACTLY 5 bullets. Keep only actionable performance items. Format as '- ...' and keep each bullet short." > $@

security.sum.md: security.md
	@cat security.md | $(ASK) "Summarize into EXACTLY 5 bullets. Keep only actionable security items. Format as '- ...' and keep each bullet short." > $@

# Phase 3 - CONCAT REPORT (no LLM)
concatenated.md: quality.sum.md perf.sum.md security.sum.md
	@{ \
		echo "## Code Quality"; \
		cat quality.sum.md; \
		echo ""; \
		echo "## Performance"; \
		cat perf.sum.md; \
		echo ""; \
		echo "## Security"; \
		cat security.sum.md; \
	} > $@

# Phase 4 - FAN-IN #1 (LLM Refine)
refined.md: concatenated.md
	@cat concatenated.md | $(ASK) "Refine this report by removing duplicates, merging overlaps, and keeping only high-signal actionable issues. Preserve these sections: Code Quality, Performance, Security. Keep concise bullet points." > $@

# Phase 5 - FAN-IN #2 (Final Engineering Action Plan)
action.plan.md: refined.md
	@cat refined.md | $(ASK) "Create a final 'Engineering Action Plan' in markdown. Include prioritized actions (High/Medium/Low), effort estimate for each action (Small/Medium/Large), and recommended execution order. Keep the plan concrete and implementation-oriented." > $@

clean:
	@rm -f quality.md perf.md security.md quality.sum.md perf.sum.md security.sum.md concatenated.md refined.md action.plan.md
