import React, { useState, useEffect } from 'react';
import { motion } from 'framer-motion';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Sun, Moon, Trash2, PlusCircle, ChevronUp, ChevronDown } from 'lucide-react';
import { Card, CardContent } from '@/components/ui/card';

export default function App() {
  const [darkMode, setDarkMode] = useState(false);
  const [gutMatrix, setGutMatrix] = useState([]);
  const [newProblem, setNewProblem] = useState({ description: '', G: 1, U: 1, T: 1 });

  useEffect(() => {
    const storedMode = localStorage.getItem('darkMode');
    if (storedMode) setDarkMode(JSON.parse(storedMode));
  }, []);

  useEffect(() => {
    localStorage.setItem('darkMode', JSON.stringify(darkMode));
  }, [darkMode]);

  const updateNumber = (key, increment) => {
    setNewProblem((prev) => {
      const newValue = Math.min(5, Math.max(1, prev[key] + (increment ? 1 : -1)));
      return { ...prev, [key]: newValue };
    });
  };

  const addProblemToGUT = () => {
    if (!newProblem.description.trim()) return;
    const priority = newProblem.G * newProblem.U * newProblem.T;
    setGutMatrix([...gutMatrix, { ...newProblem, priority }]);
    setNewProblem({ description: '', G: 1, U: 1, T: 1 });
  };

  const removeProblemFromGUT = (index) => {
    setGutMatrix(gutMatrix.filter((_, i) => i !== index));
  };

  return (
    <div className={`min-h-screen flex flex-col items-center justify-center p-10 transition-all duration-500 ${darkMode ? 'bg-gray-900 text-white' : 'bg-gray-50 text-black'}`}>
      <motion.div 
        initial={{ opacity: 0, y: -50 }} 
        animate={{ opacity: 1, y: 0 }} 
        transition={{ duration: 0.8 }}
        className="p-10 bg-white shadow-2xl rounded-3xl text-center max-w-3xl w-full border border-gray-200 relative overflow-hidden"
      >
        <Button onClick={() => setDarkMode(!darkMode)} className="absolute top-4 right-4 shadow-lg hover:scale-110 transition-transform bg-gray-100 p-2 rounded-full">
          {darkMode ? <Sun className="text-yellow-500" /> : <Moon className="text-blue-500" />}
        </Button>
        
        <h2 className="text-5xl font-extrabold mb-6 text-blue-600 tracking-wide drop-shadow-lg">Matriz GUT</h2>
        <p className="text-lg text-gray-500 mb-4">A ferramenta para priorização de problemas</p>
        <Card className="p-6 mb-6 shadow-xl border border-gray-300 bg-gradient-to-br from-gray-50 to-gray-100 rounded-2xl">
          <CardContent>
            <Input 
              placeholder="Descreva o problema" 
              value={newProblem.description} 
              onChange={(e) => setNewProblem({ ...newProblem, description: e.target.value })} 
              className="mb-4 border-gray-300 focus:border-blue-500 focus:ring focus:ring-blue-200 text-lg p-4 rounded-xl shadow-md bg-white"
            />
            <div className="flex justify-between mb-4 space-x-4">
              {['G', 'U', 'T'].map((key, index) => (
                <div key={index} className="w-1/3 flex flex-col items-center bg-white rounded-xl shadow-md border border-gray-300 p-3">
                  <Button onClick={() => updateNumber(key, true)} className="bg-blue-500 text-white rounded-t-xl px-6 py-2 hover:bg-blue-600 shadow-md">
                    <ChevronUp size={24} />
                  </Button>
                  <div className="text-center text-4xl font-extrabold py-4 w-full bg-gray-100 rounded-md shadow-inner border border-gray-300">
                    {newProblem[key]}
                  </div>
                  <Button onClick={() => updateNumber(key, false)} className="bg-blue-500 text-white rounded-b-xl px-6 py-2 hover:bg-blue-600 shadow-md">
                    <ChevronDown size={24} />
                  </Button>
                </div>
              ))}
            </div>
            <Button onClick={addProblemToGUT} className="w-full bg-gradient-to-r from-green-500 to-green-700 text-white hover:from-green-600 hover:to-green-800 flex items-center justify-center gap-3 py-4 text-lg font-semibold rounded-xl shadow-lg hover:scale-105 transition-transform border border-green-800">
              <PlusCircle size={28} className="animate-pulse" /> <span className="uppercase tracking-wide">Adicionar Problema</span>
            </Button>
          </CardContent>
        </Card>
        
        <ul className="space-y-4 w-full">
          {gutMatrix.sort((a, b) => b.priority - a.priority).map((problem, index) => (
            <Card key={index} className="p-4 flex justify-between items-center bg-white hover:bg-gray-100 rounded-2xl shadow-lg border border-gray-300 transition-all duration-300 transform hover:scale-105">
              <span className="font-medium text-lg text-gray-800 drop-shadow-md">{problem.description} - <span className="text-blue-600 font-bold">Prioridade: {problem.priority}</span></span>
              <Button onClick={() => removeProblemFromGUT(index)} variant="ghost" className="text-red-500 hover:text-red-700 transition-transform transform hover:scale-110">
                <Trash2 />
              </Button>
            </Card>
          ))}
        </ul>
      </motion.div>
    </div>
  );
}
