# STM32 AI Code Review Appendix

**Version:** 1.0  

- Skip automatically generated STM32CubeMX / CubeIDE code. Do not report style, formatting, naming, structural, or refactoring issues in generated sections.

- In files containing `USER CODE BEGIN` / `USER CODE END` markers, review only changed code within matching sections. Do not suggest modifications outside these sections, as they may be overwritten during regeneration.

- Skip all vendor-provided and third-party library code, including HAL, LL, CMSIS, middleware, and other external dependencies, regardless of their location. Do not review library source files unless they have been modified.

- Treat all explicit requirements in EMBEDDED_C_CODING_STYLE.md as review criteria for manually written code, including stylistic requirements that do not affect runtime behavior. Report violations as findings.

- Generated code may be inspected for context but must not be reported as a finding. If user code issues originate from CubeMX configuration, recommend correcting the configuration and regenerating the project instead of modifying generated code.