import React, { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { format, addDays, setHours, setMinutes } from "date-fns";

const horariosDisponiveis = [];
for (let hora = 7; hora < 12; hora++) {
  horariosDisponiveis.push(`${hora}:00`);
}
for (let hora = 13; hora < 17; hora++) {
  horariosDisponiveis.push(`${hora}:00`);
}

export default function Agendamento() {
  const [nome, setNome] = useState("");
  const [horarios, setHorarios] = useState(horariosDisponiveis);
  const [agendamentos, setAgendamentos] = useState([]);

  useEffect(() => {
    const storedAgendamentos = JSON.parse(localStorage.getItem("agendamentos")) || [];
    setAgendamentos(storedAgendamentos);
    const horariosOcupados = storedAgendamentos.map(a => a.horario);
    setHorarios(horariosDisponiveis.filter(h => !horariosOcupados.includes(h)));
  }, []);

  const agendar = (horario) => {
    if (!nome.trim()) {
      alert("Por favor, insira seu nome antes de agendar.");
      return;
    }
    const novoAgendamento = { nome, horario };
    const novosAgendamentos = [...agendamentos, novoAgendamento];
    setAgendamentos(novosAgendamentos);
    setHorarios(horarios.filter(h => h !== horario));
    localStorage.setItem("agendamentos", JSON.stringify(novosAgendamentos));
    alert(`Treinamento agendado para ${horario} com ${nome}`);
    setNome("");
  };

  return (
    <div className="p-6 max-w-lg mx-auto">
      <h1 className="text-2xl font-bold mb-4">Agendamento de Treinamento</h1>
      <Input 
        type="text" 
        placeholder="Digite seu nome" 
        value={nome} 
        onChange={(e) => setNome(e.target.value)} 
        className="mb-4"
      />
      <div className="grid grid-cols-2 gap-4">
        {horarios.length > 0 ? horarios.map((horario) => (
          <Card key={horario} className="p-2">
            <CardContent>
              <p className="text-lg font-semibold">{horario}</p>
              <Button className="mt-2 w-full" onClick={() => agendar(horario)}>
                Agendar
              </Button>
            </CardContent>
          </Card>
        )) : <p>Todos os horários estão preenchidos.</p>}
      </div>
    </div>
  );
}
