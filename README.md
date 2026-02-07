import React, { useState, useEffect } from 'react';
import { 
  BookOpen, 
  Upload, 
  Heart, 
  MessageCircle, 
  Search, 
  User, 
  X, 
  Plus, 
  Filter, 
  Star,
  Send
} from 'lucide-react';

// Data Mock Awal (Contoh Komik)
const initialComics = [
  {
    id: 1,
    title: "Pendekar Bayangan",
    author: "Raka Adhitama",
    genre: "Action",
    cover: "https://images.unsplash.com/photo-1612036782180-6f0b6cd846fe?auto=format&fit=crop&q=80&w=600",
    description: "Di dunia di mana bayangan memiliki kehendak sendiri, seorang pendekar muda harus menguasai seni kuno untuk menyelamatkan desanya dari kegelapan abadi.",
    likes: 1240,
    comments: [
      { user: "Budi_Gamer", text: "Art style-nya keren banget bang!" },
      { user: "Sari99", text: "Plot twist di chapter 3 bikin merinding." }
    ]
  },
  {
    id: 2,
    title: "Cinta di Kedai Kopi",
    author: "Luna Maya",
    genre: "Romance",
    cover: "https://images.unsplash.com/photo-1511920170033-f8396924c348?auto=format&fit=crop&q=80&w=600",
    description: "Kisah manis antara barista yang pendiam dan pelanggan tetap yang selalu memesan kopi pahit. Apakah rasa pahit itu akan berubah menjadi manis?",
    likes: 856,
    comments: [
      { user: "KopiLover", text: "Relate banget sama ceritanya." }
    ]
  },
  {
    id: 3,
    title: "Galaksi Terlupakan",
    author: "Zeta Prime",
    genre: "Sci-Fi",
    cover: "https://images.unsplash.com/photo-1451187580459-43490279c0fa?auto=format&fit=crop&q=80&w=600",
    description: "Tahun 3055, manusia telah melupakan Bumi. Sebuah kru penjelajah menemukan koordinat planet asal yang ternyata menyimpan rahasia kelam.",
    likes: 2100,
    comments: []
  },
  {
    id: 4,
    title: "Hantu Asrama Putri",
    author: "Misteri Jkt",
    genre: "Horror",
    cover: "https://images.unsplash.com/photo-1505635552518-3448ff116af3?auto=format&fit=crop&q=80&w=600",
    description: "Jangan pernah mengetuk pintu kamar nomor 13 lewat tengah malam. Konon, penghuninya tidak suka diganggu.",
    likes: 543,
    comments: [
      { user: "Penakut123", text: "Bacanya siang-siang aja deh..." }
    ]
  },
];

const categories = ["Semua", "Action", "Romance", "Comedy", "Horror", "Sci-Fi", "Slice of Life"];

export default function App() {
  const [view, setView] = useState('home'); // home, detail, upload
  const [comics, setComics] = useState(initialComics);
  const [selectedComic, setSelectedComic] = useState(null);
  const [searchQuery, setSearchQuery] = useState("");
  const [selectedCategory, setSelectedCategory] = useState("Semua");
  
  // State untuk Form Upload
  const [newComic, setNewComic] = useState({
    title: "",
    author: "",
    genre: "Action",
    cover: "",
    description: ""
  });

  // Filter Logic
  const filteredComics = comics.filter(comic => {
    const matchesSearch = comic.title.toLowerCase().includes(searchQuery.toLowerCase()) || 
                          comic.author.toLowerCase().includes(searchQuery.toLowerCase());
    const matchesCategory = selectedCategory === "Semua" || comic.genre === selectedCategory;
    return matchesSearch && matchesCategory;
  });

  // Handle Likes
  const handleLike = (id, e) => {
    e.stopPropagation();
    setComics(comics.map(comic => 
      comic.id === id ? { ...comic, likes: comic.likes + 1 } : comic
    ));
    if (selectedComic && selectedComic.id === id) {
      setSelectedComic({ ...selectedComic, likes: selectedComic.likes + 1 });
    }
  };

  // Handle Comment Submit
  const handleCommentSubmit = (e, comicId, text) => {
    e.preventDefault();
    if (!text.trim()) return;

    const newComment = { user: "Pengguna_Anonim", text: text };
    
    const updatedComics = comics.map(c => {
      if (c.id === comicId) {
        return { ...c, comments: [...c.comments, newComment] };
      }
      return c;
    });

    setComics(updatedComics);
    if (selectedComic) {
      setSelectedComic({ ...selectedComic, comments: [...selectedComic.comments, newComment] });
    }
  };

  // Handle New Comic Submit
  const handleUpload = (e) => {
    e.preventDefault();
    const id = comics.length + 1;
    // Gunakan placeholder jika cover kosong
    const coverUrl = newComic.cover || "https://images.unsplash.com/photo-1544716278-ca5e3f4abd8c?auto=format&fit=crop&q=80&w=600";
    
    const submittedComic = {
      id,
      ...newComic,
      cover: coverUrl,
      likes: 0,
      comments: []
    };

    setComics([submittedComic, ...comics]);
    setView('home');
    setNewComic({ title: "", author: "", genre: "Action", cover: "", description: "" });
  };

  return (
    <div className="min-h-screen bg-slate-900 text-slate-100 font-sans selection:bg-purple-500 selection:text-white">
      
      {/* --- Navbar --- */}
      <nav className="sticky top-0 z-50 bg-slate-900/90 backdrop-blur-md border-b border-slate-800">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex items-center justify-between h-16">
            <div className="flex items-center cursor-pointer" onClick={() => setView('home')}>
              <BookOpen className="h-8 w-8 text-purple-500 mr-2" />
              <span className="text-xl font-bold bg-gradient-to-r from-purple-400 to-pink-500 text-transparent bg-clip-text">
                KomikVerse
              </span>
            </div>
            <div className="flex items-center space-x-4">
              <button 
                onClick={() => setView('upload')}
                className="hidden sm:flex items-center px-4 py-2 rounded-full bg-purple-600 hover:bg-purple-700 transition-all text-sm font-medium"
              >
                <Upload className="h-4 w-4 mr-2" />
                Upload Karya
              </button>
              <div className="h-8 w-8 rounded-full bg-slate-700 flex items-center justify-center cursor-pointer hover:bg-slate-600">
                <User className="h-5 w-5 text-slate-300" />
              </div>
            </div>
          </div>
        </div>
      </nav>

      {/* --- Main Content Switcher --- */}
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        
        {view === 'home' && (
          <>
            {/* Hero Section */}
            <div className="mb-12 text-center py-10 bg-gradient-to-b from-slate-800 to-slate-900 rounded-3xl border border-slate-700 relative overflow-hidden">
              <div className="absolute top-0 left-0 w-full h-1 bg-gradient-to-r from-purple-500 via-pink-500 to-blue-500"></div>
              <h1 className="text-4xl md:text-5xl font-extrabold mb-4 text-white">
                Temukan Dunia Baru
              </h1>
              <p className="text-slate-400 text-lg max-w-2xl mx-auto mb-8">
                Platform komunitas untuk membaca, berbagi, dan mendiskusikan komik indie terbaik karya anak bangsa.
              </p>
              
              {/* Search & Filter Bar */}
              <div className="max-w-3xl mx-auto flex flex-col md:flex-row gap-4 px-4">
                <div className="relative flex-grow">
                  <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-slate-400 h-5 w-5" />
                  <input 
                    type="text" 
                    placeholder="Cari judul komik atau penulis..." 
                    className="w-full pl-10 pr-4 py-3 bg-slate-950 border border-slate-700 rounded-xl focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none transition-all text-slate-200"
                    value={searchQuery}
                    onChange={(e) => setSearchQuery(e.target.value)}
                  />
                </div>
                <button 
                  onClick={() => setView('upload')}
                  className="sm:hidden w-full flex items-center justify-center px-4 py-3 rounded-xl bg-purple-600 hover:bg-purple-700 font-bold"
                >
                  <Upload className="h-5 w-5 mr-2" /> Upload
                </button>
              </div>

              {/* Category Pills */}
              <div className="flex flex-wrap justify-center gap-2 mt-6 px-2">
                {categories.map(cat => (
                  <button
                    key={cat}
                    onClick={() => setSelectedCategory(cat)}
                    className={`px-4 py-1.5 rounded-full text-sm font-medium transition-colors ${
                      selectedCategory === cat 
                        ? 'bg-pink-600 text-white shadow-lg shadow-pink-500/20' 
                        : 'bg-slate-800 text-slate-400 hover:bg-slate-700 hover:text-slate-200'
                    }`}
                  >
                    {cat}
                  </button>
                ))}
              </div>
            </div>

            {/* Gallery Grid */}
            <div className="flex items-center mb-6">
              <Filter className="h-5 w-5 mr-2 text-purple-400" />
              <h2 className="text-xl font-bold">
                {selectedCategory === "Semua" ? "Karya Terbaru" : `Genre: ${selectedCategory}`}
              </h2>
            </div>

            {filteredComics.length > 0 ? (
              <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
                {filteredComics.map((comic) => (
                  <div 
                    key={comic.id}
                    onClick={() => { setSelectedComic(comic); setView('detail'); }}
                    className="group bg-slate-800 rounded-2xl overflow-hidden border border-slate-700 hover:border-purple-500/50 hover:shadow-xl hover:shadow-purple-500/10 transition-all cursor-pointer flex flex-col h-full"
                  >
                    <div className="relative aspect-[3/4] overflow-hidden">
                      <img 
                        src={comic.cover} 
                        alt={comic.title} 
                        className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500"
                      />
                      <div className="absolute top-2 right-2 bg-black/60 backdrop-blur-sm px-2 py-1 rounded-md text-xs font-semibold text-purple-300 border border-purple-500/30">
                        {comic.genre}
                      </div>
                    </div>
                    <div className="p-4 flex flex-col flex-grow">
                      <h3 className="text-lg font-bold text-white mb-1 line-clamp-1 group-hover:text-purple-400 transition-colors">{comic.title}</h3>
                      <p className="text-sm text-slate-400 mb-3 flex items-center">
                        <User className="h-3 w-3 mr-1" /> {comic.author}
                      </p>
                      <p className="text-slate-300 text-sm line-clamp-2 mb-4 flex-grow">
                        {comic.description}
                      </p>
                      <div className="flex items-center justify-between text-slate-400 text-xs border-t border-slate-700 pt-3 mt-auto">
                        <button 
                          onClick={(e) => handleLike(comic.id, e)}
                          className="flex items-center hover:text-pink-500 transition-colors"
                        >
                          <Heart className="h-4 w-4 mr-1" fill={comic.likes > 0 ? "currentColor" : "none"} />
                          {comic.likes}
                        </button>
                        <div className="flex items-center">
                          <MessageCircle className="h-4 w-4 mr-1" />
                          {comic.comments.length}
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            ) : (
              <div className="text-center py-20 text-slate-500">
                <Search className="h-12 w-12 mx-auto mb-4 opacity-20" />
                <p>Tidak ada komik yang ditemukan.</p>
              </div>
            )}
          </>
        )}

        {/* --- Upload Form View --- */}
        {view === 'upload' && (
          <div className="max-w-2xl mx-auto">
            <button 
              onClick={() => setView('home')} 
              className="mb-6 flex items-center text-slate-400 hover:text-white transition-colors"
            >
              <X className="h-5 w-5 mr-1" /> Batal
            </button>
            
            <div className="bg-slate-800 rounded-3xl p-8 border border-slate-700 shadow-2xl">
              <div className="flex items-center mb-6">
                <div className="p-3 bg-purple-600/20 rounded-xl mr-4">
                  <Upload className="h-6 w-6 text-purple-400" />
                </div>
                <div>
                  <h2 className="text-2xl font-bold text-white">Unggah Karya Baru</h2>
                  <p className="text-slate-400 text-sm">Bagikan ceritamu kepada dunia</p>
                </div>
              </div>

              <form onSubmit={handleUpload} className="space-y-6">
                <div>
                  <label className="block text-sm font-medium text-slate-300 mb-2">Judul Komik</label>
                  <input 
                    required
                    type="text" 
                    className="w-full bg-slate-900 border border-slate-600 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none"
                    placeholder="Contoh: Petualangan Si Kancil"
                    value={newComic.title}
                    onChange={e => setNewComic({...newComic, title: e.target.value})}
                  />
                </div>

                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  <div>
                    <label className="block text-sm font-medium text-slate-300 mb-2">Nama Penulis</label>
                    <input 
                      required
                      type="text" 
                      className="w-full bg-slate-900 border border-slate-600 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none"
                      placeholder="Nama Pena"
                      value={newComic.author}
                      onChange={e => setNewComic({...newComic, author: e.target.value})}
                    />
                  </div>
                  <div>
                    <label className="block text-sm font-medium text-slate-300 mb-2">Genre</label>
                    <select 
                      className="w-full bg-slate-900 border border-slate-600 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none"
                      value={newComic.genre}
                      onChange={e => setNewComic({...newComic, genre: e.target.value})}
                    >
                      {categories.filter(c => c !== "Semua").map(cat => (
                        <option key={cat} value={cat}>{cat}</option>
                      ))}
                    </select>
                  </div>
                </div>

                <div>
                  <label className="block text-sm font-medium text-slate-300 mb-2">URL Gambar Sampul (Cover)</label>
                  <div className="flex gap-2">
                    <input 
                      type="url" 
                      className="w-full bg-slate-900 border border-slate-600 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none"
                      placeholder="https://..."
                      value={newComic.cover}
                      onChange={e => setNewComic({...newComic, cover: e.target.value})}
                    />
                  </div>
                  <p className="text-xs text-slate-500 mt-2">*Gunakan link gambar langsung. Jika kosong akan menggunakan gambar default.</p>
                </div>

                <div>
                  <label className="block text-sm font-medium text-slate-300 mb-2">Sinopsis / Deskripsi</label>
                  <textarea 
                    required
                    rows="4"
                    className="w-full bg-slate-900 border border-slate-600 rounded-xl px-4 py-3 text-white focus:ring-2 focus:ring-purple-500 focus:border-transparent outline-none resize-none"
                    placeholder="Ceritakan sedikit tentang komikmu..."
                    value={newComic.description}
                    onChange={e => setNewComic({...newComic, description: e.target.value})}
                  />
                </div>

                <div className="pt-4">
                  <button 
                    type="submit" 
                    className="w-full bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700 text-white font-bold py-4 rounded-xl shadow-lg transform transition hover:-translate-y-1"
                  >
                    Terbitkan Komik
                  </button>
                </div>
              </form>
            </div>
          </div>
        )}

        {/* --- Comic Detail View --- */}
        {view === 'detail' && selectedComic && (
          <div className="animate-fade-in">
            <button 
              onClick={() => setView('home')} 
              className="mb-6 flex items-center text-slate-400 hover:text-white transition-colors"
            >
              <X className="h-5 w-5 mr-1" /> Kembali ke Beranda
            </button>

            <div className="bg-slate-800 rounded-3xl overflow-hidden border border-slate-700 shadow-2xl">
              <div className="grid grid-cols-1 md:grid-cols-3">
                {/* Left: Image */}
                <div className="h-96 md:h-auto relative bg-slate-900">
                  <img 
                    src={selectedComic.cover} 
                    alt={selectedComic.title} 
                    className="w-full h-full object-cover opacity-80"
                  />
                  <div className="absolute inset-0 bg-gradient-to-t from-slate-900 via-transparent to-transparent md:bg-gradient-to-r" />
                </div>

                {/* Right: Info */}
                <div className="col-span-2 p-8 md:p-10 flex flex-col h-full">
                  <div className="flex items-start justify-between mb-4">
                    <span className="bg-pink-600/20 text-pink-300 px-3 py-1 rounded-lg text-sm font-semibold border border-pink-500/30">
                      {selectedComic.genre}
                    </span>
                    <div className="flex space-x-2">
                       <button onClick={(e) => handleLike(selectedComic.id, e)} className="p-2 bg-slate-700 rounded-full hover:bg-pink-600/20 hover:text-pink-500 transition-colors">
                         <Heart className="h-6 w-6" fill={selectedComic.likes > 0 ? "currentColor" : "none"}/>
                       </button>
                    </div>
                  </div>

                  <h1 className="text-4xl font-bold text-white mb-2">{selectedComic.title}</h1>
                  <p className="text-lg text-purple-400 mb-6 flex items-center font-medium">
                    <User className="h-5 w-5 mr-2" /> {selectedComic.author}
                  </p>

                  <div className="mb-8">
                    <h3 className="text-slate-300 font-semibold mb-2">Sinopsis</h3>
                    <p className="text-slate-400 leading-relaxed text-lg">
                      {selectedComic.description}
                    </p>
                  </div>

                  <div className="flex items-center space-x-6 text-slate-400 text-sm border-t border-slate-700/50 pt-6 mt-auto">
                     <div className="flex items-center">
                        <Heart className="h-5 w-5 text-pink-500 mr-2" />
                        <span className="font-bold text-slate-200 text-base">{selectedComic.likes}</span>
                        <span className="ml-1">Suka</span>
                     </div>
                     <div className="flex items-center">
                        <MessageCircle className="h-5 w-5 text-blue-400 mr-2" />
                        <span className="font-bold text-slate-200 text-base">{selectedComic.comments.length}</span>
                        <span className="ml-1">Komentar</span>
                     </div>
                     <div className="flex items-center">
                        <Star className="h-5 w-5 text-yellow-400 mr-2" />
                        <span className="font-bold text-slate-200 text-base">4.8</span>
                        <span className="ml-1">Rating</span>
                     </div>
                  </div>
                </div>
              </div>

              {/* Comments Section */}
              <div className="bg-slate-900 p-8 md:p-10 border-t border-slate-700">
                <h3 className="text-xl font-bold mb-6 flex items-center">
                  <MessageCircle className="mr-2" /> Diskusi Pembaca
                </h3>
                
                <div className="space-y-4 mb-8 max-h-80 overflow-y-auto pr-2 custom-scrollbar">
                  {selectedComic.comments.length > 0 ? (
                    selectedComic.comments.map((comment, idx) => (
                      <div key={idx} className="bg-slate-800 p-4 rounded-xl border border-slate-700/50">
                        <div className="flex items-center mb-2">
                          <div className="h-8 w-8 rounded-full bg-gradient-to-br from-purple-500 to-indigo-500 flex items-center justify-center text-xs font-bold mr-3">
                            {comment.user.charAt(0)}
                          </div>
                          <span className="font-bold text-slate-300 text-sm">{comment.user}</span>
                        </div>
                        <p className="text-slate-400 pl-11">{comment.text}</p>
                      </div>
                    ))
                  ) : (
                    <p className="text-slate-500 italic">Belum ada komentar. Jadilah yang pertama!</p>
                  )}
                </div>

                {/* Comment Input */}
                <form 
                  onSubmit={(e) => {
                    const text = e.target.comment.value;
                    handleCommentSubmit(e, selectedComic.id, text);
                    e.target.comment.value = "";
                  }}
                  className="relative"
                >
                  <input 
                    name="comment"
                    type="text" 
                    placeholder="Tulis komentar..." 
                    className="w-full bg-slate-950 border border-slate-700 rounded-xl pl-4 pr-12 py-3 focus:ring-2 focus:ring-blue-500 outline-none text-slate-200"
                  />
                  <button 
                    type="submit"
                    className="absolute right-2 top-1/2 transform -translate-y-1/2 p-2 bg-blue-600 hover:bg-blue-700 rounded-lg text-white transition-colors"
                  >
                    <Send className="h-4 w-4" />
                  </button>
                </form>
              </div>
            </div>
          </div>
        )}
      </main>

      {/* --- Simple Footer --- */}
      <footer className="bg-slate-950 border-t border-slate-800 py-8 mt-12">
        <div className="max-w-7xl mx-auto px-4 text-center text-slate-500 text-sm">
          <p className="mb-2">&copy; 2024 KomikVerse Indonesia.</p>
          <p>Dibuat untuk para kreator lokal.</p>
        </div>
      </footer>
    </div>
  );
}
