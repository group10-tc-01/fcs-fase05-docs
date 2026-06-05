# Definir estrategia de testes para APIs e worker

As APIs da fase 5 terao testes unitarios, integrados e funcionais quando aplicavel, seguindo a organizacao da fase 4. Os testes integrados das APIs tambem devem cobrir endpoints. O `fcs-donation-worker` tera testes unitarios e integrados, sem projeto de testes funcionais.

**Consequencias**

- APIs podem ter `UnitTests`, `IntegratedTests`, `FunctionalTests` e `CommonTestsUtilities`.
- Testes integrados das APIs validam tambem o comportamento HTTP dos endpoints.
- O worker valida handlers, persistencia, idempotencia e integracoes necessarias por testes unitarios e integrados.
- O pipeline do worker nao deve configurar `functional_tests_path`.
- O threshold minimo de cobertura sera 80%.
