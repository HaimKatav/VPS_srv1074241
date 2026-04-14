# Taxonomy — Canonical Tags

> Tags must be small, stable, and meaningful. New tags require project-lead approval.

## Provenance (required, pick exactly one)
- #extracted          — pulled verbatim from a source document
- #inferred           — agent reasoning, NOT approved by a human
- #ambiguous          — flagged for review, do NOT rely on
- #approved-by-haim   — human-signed ground truth

## Status
- #proposed           — draft, awaiting approval
- #active             — current, authoritative
- #deprecated         — superseded (keep for history)

## Domain (pick one)
- #domain/strategy
- #domain/risk
- #domain/compliance
- #domain/data
- #domain/infra
- #domain/analytics
- #domain/frontend
- #domain/process

## Type
- #type/decision
- #type/spec
- #type/research
- #type/glossary
- #type/milestone
- #type/runbook
- #type/postmortem

## Criticality
- #critical/trading-rule   — affects what/when/how the bot trades; must be #approved-by-haim
- #critical/money          — touches real capital or prop firm limits
- #critical/data-integrity — parity, audit, lineage
