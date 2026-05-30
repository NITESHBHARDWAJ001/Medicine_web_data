# Medicine_web_data
import React, { useEffect, useRef, useState } from "react";
import { Link, useLocation, useNavigate } from "react-router-dom";
import {
  Pill,
  User,
  Menu,
  X,
  ShoppingBasket,
  LogOut,
  UserCircle,
  EyeClosed,
} from "lucide-react";
import { useCart } from "../../CartContext";
import logo from '../../assets/logo.avif';

const NavPill = ({ to, children, isActive, onClick }) => {
  const base =
    "relative px-5 py-2.5 text-base font-semibold text-white bg-sky-500 rounded-full shadow-md transition-all duration-300 transform";
  const active = "bg-sky-700 scale-105 ring-2 ring-sky-300";
  const hover =
    "hover:bg-sky-600 hover:shadow-lg hover:scale-105 active:scale-95";

  return (
    <Link
      to={to}
      onClick={onClick}
      className={`${base} ${hover} ${isActive ? active : ""}`}
      aria-current={isActive ? "page" : undefined}
    >
      {children}
    </Link>
  );
};

const Navbar = () => {
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [isDropdownOpen, setIsDropdownOpen] = useState(false);
  const [isLoggedIn, setIsLoggedIn] = useState(
    localStorage.getItem("isLoggedIn") === "true",
  );
  const [userName, setUserName] = useState(
    localStorage.getItem("userName") || "",
  );

  const { getTotalItems } = useCart();
  const cartCount = getTotalItems();

  const location = useLocation();
  const navigate = useNavigate();
  const menuRef = useRef(null);
  const dropdownRef = useRef(null);

  const toggleMenu = () => setIsMenuOpen((s) => !s);
  const toggleDropdown = () => setIsDropdownOpen((s) => !s);

  useEffect(() => {
    setIsMenuOpen(false);
    setIsLoggedIn(localStorage.getItem("isLoggedIn") === "true");
    setUserName(localStorage.getItem("userName") || "");
  }, [location.pathname]);

  useEffect(() => {
    const handleStorageChange = () => {
      setIsLoggedIn(localStorage.getItem("isLoggedIn") === "true");
      setUserName(localStorage.getItem("userName") || "");
    };
    window.addEventListener("storage", handleStorageChange);
    return () => window.removeEventListener("storage", handleStorageChange);
  }, []);

  useEffect(() => {
    const onResize = () => {
      if (window.innerWidth >= 1024) setIsMenuOpen(false);
    };
    window.addEventListener("resize", onResize);
    return () => window.removeEventListener("resize", onResize);
  }, []);

  useEffect(() => {
    const handler = (e) => {
      if (!isMenuOpen) return;
      if (menuRef.current && !menuRef.current.contains(e.target)) {
        setIsMenuOpen(false);
      }
    };
    document.addEventListener("mousedown", handler);
    return () => document.removeEventListener("mousedown", handler);
  }, [isMenuOpen]);

  useEffect(() => {
    const handler = (e) => {
      if (!isDropdownOpen) return;
      if (dropdownRef.current && !dropdownRef.current.contains(e.target)) {
        setIsDropdownOpen(false);
      }
    };
    document.addEventListener("mousedown", handler);
    return () => document.removeEventListener("mousedown", handler);
  }, [isDropdownOpen]);

  useEffect(() => {
    if (isMenuOpen) {
      document.body.style.overflow = "hidden";
    } else {
      document.body.style.overflow = "";
    }

    return () => {
      document.body.style.overflow = "";
    };
  }, [isMenuOpen]);

  const isActive = (to) => {
    if (to === "/") return location.pathname === "/";
    return location.pathname.startsWith(to);
  };

  const handleLogout = () => {
    localStorage.removeItem("isLoggedIn");
    localStorage.removeItem("userName");
    localStorage.removeItem("userEmail");
    setIsLoggedIn(false);
    setUserName("");
    setIsDropdownOpen(false);
    navigate("/login");
  };

  const displayName =
    userName.length > 20 ? `${userName.slice(0, 18)}...` : userName;

  return (
    <nav
      className="sticky top-0 z-50 w-full bg-white shadow-md"
      style={{ fontFamily: "Montserrat, sans-serif" }}
    >
      <div className="max-w-9xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between items-center h-20">
          {/* Logo */}
          <Link to="/" className="flex items-center space-x-3 group">
            <div className="w-12 h-12 rounded-full bg-linear-to-br from-sky-400 to-blue-500 p-0.5 shadow-md">
              <div className="w-full h-full rounded-full bg-white/90 flex items-center justify-center overflow-hidden">
                <img
                  src={logo}
                  alt="VitaCare Logo"
                  className="w-full h-full object-cover"
                />
              </div>
            </div>

            <span className="text-gray-800 font-black text-2xl tracking-tight">
              Vita<span className="text-sky-600">Care</span>
            </span>
          </Link>
          {/* Desktop Navigation Links */}
          <div className="hidden lg:flex items-center space-x-2">
            <NavPill to="/" isActive={isActive("/")}>
              Home
            </NavPill>
            <NavPill to="/medicines" isActive={isActive("/medicines")}>
              Medicines
            </NavPill>
            <NavPill to="/contact" isActive={isActive("/contact")}>
              Contact
            </NavPill>
            <NavPill to="/orders" isActive={isActive("/orders")}>
              My Orders
            </NavPill>
          </div>
          {/* Desktop Right Side: Cart + User Menu */}
          <div className="hidden lg:flex items-center space-x-3">
            <button
              onClick={() => navigate("/cart")}
              aria-label="Cart"
              className="relative p-2.5 cursor-pointer rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 hover:shadow-lg hover:scale-110 active:scale-95 transition-all duration-300"
            >
              <ShoppingBasket className="h-5 w-5" />
              {cartCount > 0 && (
                <span className="absolute -top-1 -right-1 bg-linear-to-br from-sky-600 to-sky-800 text-white text-xs font-bold rounded-full h-5 min-w-5 px-1 flex items-center justify-center shadow-lg">
                  {cartCount}
                </span>
              )}
            </button>

            {isLoggedIn ? (
              <div className="relative" ref={dropdownRef}>
                <button
                  onClick={toggleDropdown}
                  className="flex items-center cursor-pointer space-x-2 px-4 py-2 rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 hover:shadow-lg transition-all duration-300 focus:outline-none"
                  aria-expanded={isDropdownOpen}
                  aria-haspopup="true"
                >
                  <UserCircle className="h-5 w-5" />
                  <span className="font-semibold">{displayName}</span>
                  <EyeClosed
                    className={`h-4 w-4 transition-transform duration-200 ${isDropdownOpen ? "rotate-180" : ""}`}
                  />
                </button>

                {isDropdownOpen && (
                  <div className="absolute right-0 mt-2 w-48 bg-sky-500 rounded-xl shadow-xl py-1 z-20 border border-gray-200">
                    <Link
                      to="/profile"
                      onClick={() => setIsDropdownOpen(false)}
                      className="flex items-center hover:bg-sky-300 hover:rounded-xl cursor-pointer px-4 py-2 text-sm text-white transition-colors"
                    >
                      <UserCircle className="h-4 w-4 mr-2" />
                      Profile
                    </Link>
                    <button
                      onClick={handleLogout}
                      className="flex cursor-pointer hover:bg-sky-300 hover:rounded-xl items-center w-full text-left px-4 py-2 text-sm text-white transition-colors"
                    >
                      <LogOut className="h-4 w-4 mr-2" />
                      Logout
                    </button>
                  </div>
                )}
              </div>
            ) : (
              <button
                onClick={() => navigate("/login")}
                className="p-2.5 cursor-pointer rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 hover:shadow-lg hover:scale-110 active:scale-95 transition-all duration-300"
              >
                <User className="h-5 w-5" />
              </button>
            )}
          </div>
          {/* Mobile / Tablet Menu Button */}
          <div className="lg:hidden flex items-center">
            <button
              onClick={toggleMenu}
              aria-expanded={isMenuOpen}
              aria-controls="mobile-menu"
              className="p-2.5 rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 transition-all duration-300"
            >
              {isMenuOpen ? (
                <X className="h-6 w-6" />
              ) : (
                <Menu className="h-6 w-6" />
              )}
            </button>
          </div>
        </div>
      </div>

      {/* Mobile Backdrop */}
      {isMenuOpen && (
        <div
          className="fixed inset-x-0 top-20 bottom-0 z-40 bg-black/10 lg:hidden"
          onClick={() => setIsMenuOpen(false)}
          aria-hidden="true"
        />
      )}

      {/* Mobile / Tablet Menu */}
      <div
        id="mobile-menu"
        ref={menuRef}
        className={`lg:hidden fixed left-0 right-0 top-20 z-50 transition-[max-height,opacity] duration-300 ease-in-out overflow-hidden ${
          isMenuOpen ? "max-h-128 opacity-100" : "max-h-0 opacity-0"
        }`}
      >
        <div className="px-4 pt-2 pb-5 space-y-3 bg-white border-t border-gray-200 shadow-lg">
          <div className="space-y-2">
            <Link
              to="/"
              onClick={() => setIsMenuOpen(false)}
              className="block px-5 py-3 text-white font-semibold text-base bg-sky-500 rounded-full shadow-md hover:bg-sky-600 hover:shadow-lg transition-all duration-300 text-center"
            >
              Home
            </Link>
            <Link
              to="/medicines"
              onClick={() => setIsMenuOpen(false)}
              className="block px-5 py-3 text-white font-semibold text-base bg-sky-500 rounded-full shadow-md hover:bg-sky-600 hover:shadow-lg transition-all duration-300 text-center"
            >
              Medicines
            </Link>
            <Link
              to="/contact"
              onClick={() => setIsMenuOpen(false)}
              className="block px-5 py-3 text-white font-semibold text-base bg-sky-500 rounded-full shadow-md hover:bg-sky-600 hover:shadow-lg transition-all duration-300 text-center"
            >
              Contact
            </Link>
            <Link
              to="/orders"
              onClick={() => setIsMenuOpen(false)}
              className="block px-5 py-3 text-white font-semibold text-base bg-sky-500 rounded-full shadow-md hover:bg-sky-600 hover:shadow-lg transition-all duration-300 text-center"
            >
              My Orders
            </Link>
          </div>

          <div className="flex items-center justify-center space-x-4 pt-4 border-t border-gray-200">
            <button
              onClick={() => navigate("/cart")}
              className="relative p-3 rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 transition-all duration-300"
              aria-label="Mobile cart"
            >
              <ShoppingBasket className="h-5 w-5" />
              {cartCount > 0 && (
                <span className="absolute -top-1 -right-1 bg-linear-to-br from-sky-600 to-sky-800 text-white text-xs font-bold rounded-full h-5 min-w-5 px-1 flex items-center justify-center shadow-lg">
                  {cartCount}
                </span>
              )}
            </button>

            {isLoggedIn ? (
              <>
                <Link
                  to="/profile"
                  onClick={() => setIsMenuOpen(false)}
                  className="px-5 cursor-pointer py-3 rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 transition-all duration-300 font-semibold"
                >
                  Profile
                </Link>
                <button
                  onClick={() => {
                    handleLogout();
                    setIsMenuOpen(false);
                  }}
                  className="px-5 py-3 cursor-pointer rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 transition-all duration-300 font-semibold"
                  aria-label="Mobile logout"
                >
                  Logout
                </button>
              </>
            ) : (
              <button
                onClick={() => navigate("/login")}
                className="p-3 rounded-full bg-sky-500 text-white shadow-md hover:bg-sky-600 transition-all duration-300"
                aria-label="Mobile account"
              >
                <User className="h-5 w-5" />
              </button>
            )}
          </div>
        </div>
      </div>
    </nav>
  );
};

export default Navbar;
