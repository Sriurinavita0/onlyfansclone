<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tinder Clone</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css" rel="stylesheet" />
    <script src="https://unpkg.com/react/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .card {
            transition: transform 0.3s, box-shadow 0.3s;
        }
        .card:hover {
            transform: scale(1.05);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
        }
        .fade-enter {
            opacity: 0;
            transform: translateY(10px);
        }
        .fade-enter-active {
            opacity: 1;
            transform: translateY(0);
            transition: opacity 300ms, transform 300ms;
        }
    </style>
</head>

<body class="bg-gradient-to-r from-teal-400 to-blue-500 min-h-screen flex items-center justify-center p-4">
    <div id="app" class="w-full max-w-lg"></div>

    <script type="text/babel">
        const mockUsers = [
            { id: 1, name: 'Ana', age: 24, bio: 'Ama gatos e tecnologia', image: 'https://via.placeholder.com/150' },
            { id: 2, name: 'Carlos', age: 28, bio: 'Fã de aventuras e esportes', image: 'https://via.placeholder.com/150' },
            { id: 3, name: 'Bruna', age: 22, bio: 'Desenvolvedora web', image: 'https://via.placeholder.com/150' },
            { id: 4, name: 'João', age: 30, bio: 'Fã de música e cinema', image: 'https://via.placeholder.com/150' },
            { id: 5, name: 'Maria', age: 25, bio: 'Ama viajar e conhecer novos lugares', image: 'https://via.placeholder.com/150' },
        ];

        const Menu = ({ onNavigate }) => (
            <div className="absolute top-4 right-4">
                <button onClick={() => onNavigate('menu')} className="bg-white text-gray-700 p-2 rounded-full shadow-md hover:bg-gray-100">
                    <i className="fas fa-bars"></i>
                </button>
                <button onClick={() => onNavigate('dashboard')} className="bg-white text-gray-700 p-2 rounded-full shadow-md hover:bg-gray-100">
                    <i className="fas fa-chart-bar"></i>
                </button>
            </div>
        );

        const SwipeCard = ({ user, onLike, onDislike }) => (
            <div className="card bg-white p-6 rounded-lg shadow-lg text-center">
                <img src={user.image} alt={user.name} className="w-40 h-40 mx-auto rounded-full mb-4" />
                <h3 className="text-xl font-semibold">{user.name}, {user.age}</h3>
                <p className="text-gray-500 mb-4">{user.bio}</p>
                <div className="flex justify-around mt-4">
                    <button className="bg-red-500 text-white p-3 rounded-full hover:bg-red-600" onClick={() => onDislike(user.id)}>
                        <i className="fas fa-times"></i>
                    </button>
                    <button className="bg-green-500 text-white p-3 rounded-full hover:bg-green-600" onClick={() => onLike(user.id)}>
                        <i className="fas fa-heart"></i>
                    </button>
                </div>
            </div>
        );

        const MatchesList = ({ matches, onBack }) => (
            <div className="p-6 bg-white rounded-lg shadow-lg">
                <h2 className="text-xl font-semibold mb-4">Matches</h2>
                <ul className="space-y-2">
                    {matches.map(match => (
                        <li key={match.id} className="flex items-center">
                            <img src={match.image} alt={match.name} className="w-10 h-10 rounded-full mr-3" />
                            <span className="text-lg">{match.name}</span>
                        </li>
                    ))}
                </ul>
                <button className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100" onClick={onBack}>
                    Voltar
                </button>
            </div>
        );

        const Chat = ({ messages, onSendMessage }) => (
            <div className="p-6 bg-white rounded-lg shadow-lg">
                <h2 className="text-xl font-semibold mb-4">Conversa</h2>
                <ul className="space-y-2">
                    {messages.map(message => (
                        <li key={message.id} className="flex items-center">
                            <span className="text-lg">{message.text}</span>
                        </li>
                    ))}
                </ul>
                <form onSubmit={onSendMessage} className="mt-4 flex">
                    <input type="text" className="flex-1 p-3 rounded-full border" placeholder="Digite uma mensagem..." />
                    <button type="submit" className="bg-blue-500 text-white p-3 rounded-full hover:bg-blue-600 ml-2">
                        Enviar
                    </button>
                </form>
            </div>
        );

        const Dashboard = ({ matches, onNavigate }) => (
            <div className="p-6 bg-white rounded-lg shadow-lg">
                <h2 className="text-xl font-semibold mb-4">Dashboard</h2>
                <p>Bem-vindo ao seu painel de controle!</p>
                <p>Total de Matches: {matches.length}</p>
                <button className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100" onClick={() => onNavigate('chat')}>
                    Ir para Conversa
                </button>
            </div>
        );

        const App = () => {
            const [users, setUsers] = React.useState(mockUsers);
            const [matches, setMatches] = React.useState([]);
            const [messages, setMessages] = React.useState([]);
            const [currentIndex, setCurrentIndex] = React.useState(0);
            const [showMenu, setShowMenu] = React.useState(false);
            const [showMatches, setShowMatches] = React.useState(false);
            const [showChat, setShowChat] = React.useState(false);
            const [showDashboard, setShowDashboard] = React.useState(false);

            const handleLike = userId => {
                const likedUser = users.find(user => user.id === userId);
                setMatches([...matches, likedUser]);
                setUsers(users.filter(user => user.id !== userId));
            };

            const handleDislike = userId => {
                setUsers(users.filter(user => user.id !== userId));
            };

            const handleSendMessage = event => {
                event.preventDefault();
                const messageText = event.target.elements[0].value;
                setMessages([...messages, { id: messages.length + 1, text: messageText }]);
                event.target.elements[0].value = '';
            };

            const handleNavigate = route => {
                setShowMenu(false);
                setShowMatches(false);
                setShowChat(false);
                setShowDashboard(false);

                switch (route) {
                    case 'menu':
                        setShowMenu(true);
                        break;
                    case 'dashboard':
                        setShowDashboard(true);
                        break;
                    case 'matches':
                        setShowMatches(true);
                        break;
                    case 'chat':
                        setShowChat(true);
                        break;
                    default:
                        break;
                }
            };

            return (
                <div className="flex flex-col h-screen">
                    {showMenu && (
                        <div className="absolute top-0 left-0 w-full h-screen bg-gray-200 p-4">
                            <h2 className="text-xl font-semibold mb-4">Menu</h2>
                            <ul className="space-y-2">
                                <li>
                                    <button onClick={() => handleNavigate('dashboard')} className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100">
                                        Dashboard
                                    </button>
                                </li>
                                <li>
                                    <button onClick={() => handleNavigate('matches')} className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100">
                                        Matches
                                    </button>
                                </li>
                                <li>
                                    <button onClick={() => handleNavigate('chat')} className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100">
                                        Conversas
                                    </button>
                                </li>
                            </ul>
                            <button className="bg-white text-gray-700 p-3 rounded-full hover:bg-gray-100" onClick={() => setShowMenu(false)}>
                                Fechar Menu
                            </button>
                        </div>
                    )}
                    {showMatches && <MatchesList matches={matches} onBack={() => setShowMatches(false)} />}
                    {showChat && <Chat messages={messages} onSendMessage={handleSendMessage} />}
                    {showDashboard && <Dashboard matches={matches} onNavigate={handleNavigate} />}
                    {!showMatches && !showChat && !showDashboard && (
                        <>
                            <Menu onNavigate={handleNavigate} />
                            {users.length > 0 ? (
                                <SwipeCard user={users[currentIndex]} onLike={handleLike} onDislike={handleDislike} />
                            ) : (
                                <div className="bg-white p-6 rounded-lg shadow-lg text-center">
                                    <h3 className="text-lg font-semibold">Sem mais usuários!</h3>
                                    <p className="text-gray-500">Você já avaliou todos os usuários disponíveis.</p>
                                </div>
                            )}
                        </>
                    )}
                </div>
            );
        };

        ReactDOM.render(<App />, document.getElementById('app'));
    </script>
</body>
</html>
