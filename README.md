# SNAP_TICKET
import { useState, useEffect } from 'react';
import { UnifiedHomePage } from '@/app/components/UnifiedHomePage';
import { BusListing } from '@/app/components/BusListing';
import { BusSeatSelection } from '@/app/components/BusSeatSelection';
import { TrainListing } from '@/app/components/TrainListing';
import { TrainSeatSelection } from '@/app/components/TrainSeatSelection';
import { MovieSeatSelection } from '@/app/components/MovieSeatSelection';
import { UnifiedPayment } from '@/app/components/UnifiedPayment';
import { UnifiedBookingConfirmation } from '@/app/components/UnifiedBookingConfirmation';
import { initializeMockData, Bus, Train } from '@/app/data/unifiedMockData';

type View = 'home' | 'busListing' | 'trainListing' | 'movieSeats' | 'busSeats' | 'trainSeats' | 'payment' | 'confirmation';

function App() {
  const [currentView, setCurrentView] = useState<View>('home');
  const [bookingType, setBookingType] = useState<'movie' | 'bus' | 'train'>('movie');
  
  // Selected items
  const [selectedBus, setSelectedBus] = useState<Bus | null>(null);
  const [selectedTrain, setSelectedTrain] = useState<Train | null>(null);
  const [selectedClass, setSelectedClass] = useState<string>('');
  
  // Booking details
  const [selectedSeats, setSelectedSeats] = useState<string[]>([]);
  const [totalAmount, setTotalAmount] = useState(0);
  const [bookingDetails, setBookingDetails] = useState<any>(null);

  useEffect(() => {
    // Initialize mock data on mount
    initializeMockData();
  }, []);

  const handleCategorySelect = (category: 'movie' | 'bus' | 'train') => {
    setBookingType(category);
    
    if (category === 'bus') {
      setCurrentView('busListing');
    } else if (category === 'train') {
      setCurrentView('trainListing');
    } else {
      // For movie, we would show movie listing here
      // For now, let's show a simple seat selection as demo
      setCurrentView('movieSeats');
    }
  };

  const handleBusSelect = (bus: Bus) => {
    setSelectedBus(bus);
    setCurrentView('busSeats');
  };

  const handleTrainSelect = (train: Train, trainClass: string) => {
    setSelectedTrain(train);
    setSelectedClass(trainClass);
    setCurrentView('trainSeats');
  };

  const handleBusSeatsConfirm = (seats: string[], price: number) => {
    setSelectedSeats(seats);
    setTotalAmount(price);
    setBookingDetails({
      operator: selectedBus?.operator,
      busNumber: selectedBus?.busNumber,
      busType: selectedBus?.busType,
      from: selectedBus?.from,
      to: selectedBus?.to,
      date: selectedBus?.date,
      departureTime: selectedBus?.departureTime,
      seats: seats,
      totalAmount: price
    });
    setCurrentView('payment');
  };

  const handleTrainSeatsConfirm = (seats: string[], price: number) => {
    setSelectedSeats(seats);
    setTotalAmount(price);
    setBookingDetails({
      trainName: selectedTrain?.trainName,
      trainNumber: selectedTrain?.trainNumber,
      from: selectedTrain?.from,
      to: selectedTrain?.to,
      date: selectedTrain?.date,
      departureTime: selectedTrain?.departureTime,
      class: selectedClass,
      seats: seats,
      totalAmount: price
    });
    setCurrentView('payment');
  };

  const handleMovieSeatsConfirm = (seats: string[], price: number) => {
    setSelectedSeats(seats);
    setTotalAmount(price);
    setBookingDetails({
      movieTitle: 'Sample Movie',
      theatreName: 'Sample Theatre',
      theatreLocation: 'Sample Location',
      showDate: '2026-01-20',
      showTime: '06:00 PM',
      seats: seats,
      totalAmount: price
    });
    setCurrentView('payment');
  };

  const handlePaymentComplete = () => {
    setCurrentView('confirmation');
  };

  const handleBackToHome = () => {
    setCurrentView('home');
    setSelectedBus(null);
    setSelectedTrain(null);
    setSelectedClass('');
    setSelectedSeats([]);
    setTotalAmount(0);
    setBookingDetails(null);
  };

  return (
    <>
      {currentView === 'home' && (
        <UnifiedHomePage onSelectCategory={handleCategorySelect} />
      )}

      {currentView === 'busListing' && (
        <BusListing 
          onBack={handleBackToHome}
          onSelectBus={handleBusSelect}
        />
      )}

      {currentView === 'busSeats' && selectedBus && (
        <BusSeatSelection
          bus={selectedBus}
          onBack={() => setCurrentView('busListing')}
          onContinue={handleBusSeatsConfirm}
        />
      )}

      {currentView === 'trainListing' && (
        <TrainListing
          onBack={handleBackToHome}
          onSelectTrain={handleTrainSelect}
        />
      )}

      {currentView === 'trainSeats' && selectedTrain && (
        <TrainSeatSelection
          train={selectedTrain}
          selectedClass={selectedClass}
          onBack={() => setCurrentView('trainListing')}
          onContinue={handleTrainSeatsConfirm}
        />
      )}

      {currentView === 'movieSeats' && (
        <MovieSeatSelection
          movie={{ title: 'Sample Movie', duration: '2h 30m' }}
          theatre={{ name: 'PVR Cinemas', location: 'Anna Nagar' }}
          showtime={{ time: '06:00 PM', price: 200, date: '2026-01-20' }}
          onBack={handleBackToHome}
          onContinue={handleMovieSeatsConfirm}
        />
      )}

      {currentView === 'payment' && bookingDetails && (
        <UnifiedPayment
          bookingType={bookingType}
          bookingDetails={bookingDetails}
          totalAmount={totalAmount}
          onBack={() => {
            if (bookingType === 'bus') setCurrentView('busSeats');
            else if (bookingType === 'train') setCurrentView('trainSeats');
            else setCurrentView('movieSeats');
          }}
          onComplete={handlePaymentComplete}
        />
      )}

      {currentView === 'confirmation' && bookingDetails && (
        <UnifiedBookingConfirmation
          bookingType={bookingType}
          bookingDetails={bookingDetails}
          onBackToHome={handleBackToHome}
        />
      )}
    </>
  );
}

export default App;
