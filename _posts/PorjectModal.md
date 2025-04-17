import React from "react";
import {X, ChevronRight, ChevronLeft} from "lucide-react";
import {useRef} from "react";
import Image from "next/image";

export default function ProjectModal({project, onClose, onNext, onPrev}) {
    const modalRef = useRef();

    const handleOutsideClick = (e) => {
        if (modalRef.current === e.target) {
            onClose();
        }
    };

    return(
        <div
        className = "fixed inset-0 bg-gray-800 bg-opacity-75 flex items-center justify-center z-50"
        onClick = {handleOutsideClick}
        ref = {modalRef}>
            <div
            ref = {modalRef}
            className = "bg-gray-900 rounded-lg max-w-4xl w-11/12 max-h-[90vh] overflow-y-auto p-12 h-10"
            onClick = {(e) => e.stopPropagation()}>
                <div className="flex justify-between mb-3">
                    <h2 className="text-2xl font-bold text-white">{project.title}</h2>
                    <button
                    onClick = {onClose}
                    className = "text-white hover:text-white rounded-full p-2 bg-gray-800 hover:bg-gray-800/70 transition-colors">
                        <X size={24} />
                    </button>
                </div>
                <div className="flex flex-col md:flex-row gap-6">
                    <div className = "space-y-4 md:w-1/2">
                    <p className="text-gray-300">
                        {project.description}
                    </p>
                    <div>
                    <h3 className = "font-semibold mb-2 text-white">Doggie Tags:</h3>
                    <ul className="list-disc list-inside">
                        {project.tag.map((eachTag, index) =>(
                            <li
                            key={index}
                            className ="text-gray-300">
                                {eachTag}
                            </li>
                        ))}
                    </ul>
                    </div>
                </div>
            <div className = "relative h-64 w-full md:w-1/2">
            <img 
            src ={project.image}
            alt={project.title}
            style={{ 
                width: '100%', 
                height: '100%', 
                objectFit: 'contain' 
              }} 
            />
            </div>
            <div className = "absolute top-1/2 -translate-y-1/2 flex justify-between w-11/12 max-w-4xl">
            <button
            onClick={onPrev}
            className ="bg-gray-800/50 rounded-full p-2 hover:bg-gray-800/70 transition-colors -translate-x-full">
                <ChevronLeft size={24} />
            </button>
            <button
            onClick={onNext}
            className ="bg-gray-800/50 rounded-full p-2 hover:bg-gray-800/70 transition-colors -translate-x-full">
                <ChevronRight size={24} />
            </button>
            </div>
        </div>
        </div>
    </div>
    );

}
