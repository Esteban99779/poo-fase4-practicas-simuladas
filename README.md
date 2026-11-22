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
"""
Módulo principal de dominio en Programación Orientada a Objetos.
Gestiona una sala o recurso reservable validando datos y capturando/relanzando errores.
"""

from excepciones import (
    CapacidadInsuficienteError,
    EntradaInvalidaError,
    OperacionNoPermitidaError,
)


class SalaRecurso:
    """Representa un recurso con capacidad limitada y control de cupos ocupados."""

    def __init__(self, codigo: str, nombre: str, capacidad_total: int) -> None:
        # Validamos parámetros al construir el objeto
        self.codigo = self._validar_cadena(codigo, "codigo")
        self.nombre = self._validar_cadena(nombre, "nombre")
        self.capacidad_total = self._validar_entero_positivo(capacidad_total, "capacidad_total")
        self.cupos_ocupados = 0
        self.activa = True

    @staticmethod
    def _validar_cadena(valor: str, campo: str) -> str:
        """Verifica que el valor sea un texto no vacío."""
        if not isinstance(valor, str) or not valor.strip():
            raise EntradaInvalidaError(f"El campo '{campo}' no puede estar vacío.")
        return valor.strip()

    @staticmethod
    def _validar_entero_positivo(valor: int, campo: str) -> int:
        """Verifica que el valor sea un entero mayor que cero."""
        if isinstance(valor, bool) or not isinstance(valor, int) or valor <= 0:
            raise EntradaInvalidaError(f"El campo '{campo}' debe ser un entero mayor que 0.")
        return valor

    @property
    def cupos_disponibles(self) -> int:
        """Calcula los cupos libres actuales."""
        return self.capacidad_total - self.cupos_ocupados

    def reservar(self, cantidad: int) -> int:
        """
        Registra una reserva si el recurso está activo y hay cupos suficientes.
        Retorna la cantidad de cupos disponibles tras la operación.
        """
        if not self.activa:
            raise OperacionNoPermitidaError(
                f"No se pueden realizar reservas: el recurso '{self.nombre}' está inactivo."
            )

        cantidad_valida = self._validar_entero_positivo(cantidad, "cantidad")

        if cantidad_valida > self.cupos_disponibles:
            raise CapacidadInsuficienteError(
                f"Cupos insuficientes en '{self.nombre}': "
                f"disponibles {self.cupos_disponibles}, solicitados {cantidad_valida}."
            )

        self.cupos_ocupados += cantidad_valida
        return self.cupos_disponibles

    def liberar(self, cantidad: int) -> int:
        """Libera cupos previamente ocupados, validando que no superen lo ocupado."""
        cantidad_valida = self._validar_entero_positivo(cantidad, "cantidad")

        if cantidad_valida > self.cupos_ocupados:
            raise OperacionNoPermitidaError(
                f"No se pueden liberar {cantidad_valida} cupos; "
                f"solo hay {self.cupos_ocupados} ocupados en '{self.nombre}'."
            )

        self.cupos_ocupados -= cantidad_valida
        return self.cupos_disponibles

    def cambiar_estado(self, activa: bool) -> None:
        """Activa o desactiva el recurso."""
        if not isinstance(activa, bool):
            raise EntradaInvalidaError("El estado 'activa' debe ser booleano (True/False).")
        self.activa = activa
        """
Script de validación secuencial para verificar el comportamiento normal,
los casos de error y la estabilidad del sistema ante excepciones.
"""

from excepciones import (
    CapacidadInsuficienteError,
    EntradaInvalidaError,
    OperacionNoPermitidaError,
)
from gestor_reservas import SalaRecurso


def correr_pruebas() -> None:
    """Ejecuta una secuencia ordenada de pruebas y muestra el resultado de cada paso."""
    print("--- 1. Creando recurso válido ---")
    try:
        sala = SalaRecurso("LAB-101", "Laboratorio de Redes", 20)
        print(f"Recurso creado: {sala.nombre} | Disponibles: {sala.cupos_disponibles}")
    except EntradaInvalidaError as err:
        print(f"Error al crear recurso: {err}")
        return

    print("\n--- 2. Reserva válida ---")
    try:
        libres = sala.reservar(8)
        print(f"Reserva exitosa. Cupos disponibles: {libres}")
    except (EntradaInvalidaError, CapacidadInsuficienteError, OperacionNoPermitidaError) as err:
        print(f"Fallo controlado en reserva: {err}")

    print("\n--- 3. Prueba de error: superar capacidad ---")
    try:
        sala.reservar(15)
    except CapacidadInsuficienteError as err:
        print(f"Excepción capturada correctamente: {err}")

    print("\n--- 4. Prueba de error: entrada inválida (0 o negativa) ---")
    try:
        sala.reservar(-2)
    except EntradaInvalidaError as err:
        print(f"Excepción capturada correctamente: {err}")

    print("\n--- 5. Liberación parcial y recurso inactivo ---")
    try:
        libres = sala.liberar(3)
        print(f"Liberación exitosa. Cupos disponibles: {libres}")
        sala.cambiar_estado(False)
        sala.reservar(2)
    except OperacionNoPermitidaError as err:
        print(f"Excepción capturada correctamente: {err}")
    finally:
        print("Bloque finally ejecutado: ciclo de validación finalizado.")


if __name__ == "__main__":
    correr_pruebas()
    # Fase 4 – Prácticas simuladas (POO y Manejo de Excepciones)

Repositorio colaborativo del grupo para la **Fase 4 – Prácticas simuladas** del curso de Programación Orientada a Objetos.

## Estructura del proyecto
- `src/excepciones.py`: Excepciones personalizadas del dominio.
- `src/gestor_reservas.py`: Clase `SalaRecurso` con validaciones y reglas de negocio.
- `src/validacion_secuencial.py`: Pruebas secuenciales de funcionamiento y manejo de errores.

## Cómo ejecutar la validación
```bash
python src/validacion_secuencial.py
