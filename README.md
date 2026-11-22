# 1. Revisa qué archivos cambiaste o creaste
git status

# 2. Agrega los archivos al área de preparación
git add .

# 3. Crea un commit descriptivo (indica qué implementaste o corrigiste)
git commit -m "feat: implementa excepciones personalizadas y validacion secuencial"

# 4. Sube tu rama a GitHub
git push -u origin rama-excepciones-tu-nombre# poo-fase4-practicas-simuladas
git checkout main
git pull origin main
"""
Módulo de excepciones personalizadas para la Fase 4.
Define errores específicos del dominio para controlar fallos de forma explícita.
"""


class ErrorReservaBase(Exception):
    """Clase base para excepciones del sistema de reservas."""


class EntradaInvalidaError(ErrorReservaBase):
    """Se lanza cuando un dato de entrada es vacío, nulo o está fuera de rango."""


class CapacidadInsuficienteError(ErrorReservaBase):
    """Se lanza cuando se solicitan más cupos o unidades de los disponibles."""


class OperacionNoPermitidaError(ErrorReservaBase):
    """Se lanza cuando se intenta una acción inválida sobre el estado actual."""
