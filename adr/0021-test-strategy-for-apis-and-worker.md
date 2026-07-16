# Definir estratégia de testes para APIs e worker

As APIs da fase 5 terão testes unitários, integrados e funcionais quando aplicável, seguindo a organização da fase 4. Os testes integrados das APIs também devem cobrir endpoints. O `fcs-donation-worker` terá testes unitários e integrados, sem projeto de testes funcionais.

**Consequências**

- APIs podem ter `UnitTests`, `IntegratedTests`, `FunctionalTests` e `CommonTestsUtilities`.
- Testes integrados das APIs validam também o comportamento HTTP dos endpoints.
- O worker valida handlers, persistência, idempotência e integrações necessárias por testes unitários e integrados.
- O pipeline do worker não deve configurar `functional_tests_path`.
- O threshold mínimo de cobertura será 80%.
